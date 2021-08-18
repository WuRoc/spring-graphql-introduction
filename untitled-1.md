# Untitled



```text
  //弹窗---小树木
        Map<String, String> map = new HashMap<>();


        //判断是否是高中生
        List<String> highStudentIds = dataAgent.getHighStudentIds();
        boolean isHighStudent = highStudentIds.contains(studentId.toString());
        map.put("studentId", studentId.toString());
        if (isHighStudent) {
            map.put(STAGETYPE, "2");
        } else {
            map.put(STAGETYPE, "1");
        }

        //今日用户学习的单词
        long todayLearningWord = learnedCount;
        //今日用户学习的目标单词数
        long todayLearningTargetWord = goalCount;
        //学生复现词库的单词
        boolean wordsOfThesaurus = false;
        //用户学习的全部单词
        long wordsLearningOfUser = allLearnedCount;
        //全部单词数
        long wordsOfAll = allWordsCount;

        HashMap<String, Long> response = new HashMap<>();
        try {
            response = esDao.search("studied_word", map, "studyType");
        } catch (Exception e) {
            e.printStackTrace();
        }

        //判断单词列表是否包含复现词库

        //判断不为空
        if (ObjectUtils.isNotEmpty(response)) {
            if (response.containsKey(EnumStudyType.REAPPEAR_1.name()) || response.containsKey(EnumStudyType.REAPPEAR_2.name()) || response.containsKey(EnumStudyType.REAPPEAR_3.name()) || response.containsKey(EnumStudyType.REAPPEAR_4.name())) {
                wordsOfThesaurus = true;
            }
        }

        int res = 0;
        //初始化
        Map<String, Object> map1 = esDao.getResultById(IndexConstant.INDIVIDUAL_WORD_STUDENT_INFO,studentId.toString());

        if (ObjectUtils.isNotEmpty(map1)) {
            String idxAllWords ="0";
            if (map1.get(ALLOFWORD) == null) {
                map1.put(ALLOFWORD, idxAllWords);
                esDao.updatePart(IndexConstant.INDIVIDUAL_WORD_STUDENT_INFO, studentId.toString(), map1);
            }else {
                idxAllWords=map1.get(ALLOFWORD).toString();
                if (idxAllWords.equals(ZERO)){
                    //判断进行今日是否完成，如果复现词库仍有单词需要进行复现，返回1，否则进行第二次判断
                    if (wordsOfThesaurus){
                        if (todayLearningWord == todayLearningTargetWord) {
                            res = 1;
                            //如果wordsOfThesaurus 是true 词库里是有复现。需要判断用户全部完成单词后。如果完成就弹 2
                            if (wordsLearningOfUser == wordsOfAll) {
                                res = 2;
                            }
                        }
                    }else {
                        //判断是否完成今日的目标单词和复现词库没有单词
//                if (todayLearningWord == todayLearningTargetWord && wordsLearningOfUser == wordsOfAll){
                        if (wordsLearningOfUser == wordsOfAll){
                            res = 3;
                            map1.put(ALLOFWORD,1);
                            esDao.updatePart(IndexConstant.INDIVIDUAL_WORD_STUDENT_INFO,studentId.toString(),map1);
                        }
                    }
                }   
            }
        }
        //String idxAllWords = esDao.getResultById(IndexConstant.INDIVIDUAL_WORD_STUDENT_INFO,studentId.toString()).get(ALLOFWORD).toString();
        //如果是零，则代表还可以弹窗。

        settlementWordResult.setPopUps(res);
```

