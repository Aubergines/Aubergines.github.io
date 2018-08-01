---
title: POJ-1007问题答案JAVA版本
tags: 算法
grammar_cjkRuby: true
---
好久没有做过算法的题了，想锻炼一下自己这快要生锈的脑袋，找了几个比较简单的题目练练手。这道题要求对给定输入的多个字符串做排序，排序的规则一：每个字符串计算其逆序数，规则二：相同的逆序数，最终排序输出先输入的先输出。先上代码

``` java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.Map;
import java.util.Scanner;
import java.util.TreeMap;

public class Main {
    private void calculNum(List<String> dataList) {
        Map resultMap = new TreeMap();
        for (String oneData : dataList) {
            int num = 0;
            String[] split = oneData.split("_");
            for (int i = 0; i < split[1].length(); i++) {
                for (int j = i + 1; j < split[1].length(); j++) {
                    if (split[1].charAt(i) > split[1].charAt(j)) {
                        num++;
                    }
                }
            }
            resultMap.put(oneData, num);
        }
        List<Map.Entry<String, Integer>> entryList = new ArrayList<Map.Entry<String, Integer>>(resultMap.entrySet());
        Collections.sort(entryList, new MapValueComparator());
        for (Map.Entry<String, Integer> stringIntegerEntry : entryList) {
            System.out.println(stringIntegerEntry.getKey().split("_")[1]);
        }
    }


    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s = sc.nextLine();
        int line = Integer.parseInt(s.split(" ")[1]);
        List<String> dataList = new ArrayList<String>();
        for (int i = 0; i < line; i++) {
            dataList.add(i + "_" +sc.nextLine());
        }
        Main dnaSort = new Main();
        dnaSort.calculNum(dataList);
    }

    class MapValueComparator implements Comparator<Map.Entry<String, Integer>> {
        @Override
        public int compare(Map.Entry<String, Integer> me1, Map.Entry<String, Integer> me2) {

            if (me1.getValue() > me2.getValue()) {
                return 1;
            } else if (me1.getValue().intValue() == me2.getValue().intValue()) {
                if (Integer.parseInt(me1.getKey().split("_")[0]) < Integer.parseInt(me2.getKey().split("_")[0])){
                    return -1;
                }else {
                    return 1;
                }
            } else {
                return -1;
            }
        }
    }
}
```
最开始只想用字符串，没有想到保存输入的顺序，提交的时候总是WA，后来把输入顺序加上就可以了，不过目前看这种做法有点不面向队形，推荐一篇文章[poj 1007 Java][1]，这个是使用面向对象的方式处理了。


  [1]: https://blog.csdn.net/jack_wong2010/article/details/7623076