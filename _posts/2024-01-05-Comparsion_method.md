---
layout: post
title: JAVA JDK8-Comparsion method violates its general contract
date: 2023-01-16 21:01:00
description: Comparsion method violates its general contract
tags: Coding
categories: Computer_Science
related_posts: false

toc:
 - name: Solution 1
 - name: Solution 2
 - name: Explaination
 - name: Reference
---

## Solution 1  
- Add this code into `main()`  

```java
System.setProperty("java.util.Arrays.useLegacyMergeSort", "true");
```  

- code as below.  

```java
public static void main(String[] args) throws IOException {
        // TODO Auto-generated method stub
        //system properties---------------------------------------------------
        System.setProperty("java.util.Arrays.useLegacyMergeSort", "true");
        String fileName = "K2_flu_12062022_20230111_try_correct1.xdsl";
        System.out.println("try" + fileName);
        runOne(fileName);
        System.out.println("finish!");

```
This will use the old merge sort algorithm instead of TimSort. By using this algorithm, it could bypass the EXCEPTION.  



## Solution 2  
- SepSetComparator need to be modified.  
- Comparator must be transitive without a contradiction.  
- Compare( ) should follow this rule (https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)  
- add this code to compare()  

```java
   public int compare(Object o1, Object o2) {
        SepSet sepSet1 = (SepSet) o1;
        SepSet sepSet2 = (SepSet) o2;
        int[] results1 = new int[2];
        int[] results2 = new int[2];
        results1[0] = -sepSet1.size();
        results1[1] = 1;
        for (int i = 0; i < sepSet1.size(); i++) {
            results1[1] = results1[1] * sepSet1.get(i).getStates().size();
        }
        results2[0] = -sepSet2.size();
        results2[1] = 1;
        for (int i = 0; i < sepSet2.size(); i++) {
            results2[1] = results2[1] * sepSet2.get(i).getStates().size();
        }
// Here is the modifying part.-----------------------------------------------
        if (results1[0] < results2[0] || results1[1]<=results2[1]) {
            return -1;
}
        else if (results1[0] == results2[0] || results1[1]>results2[1]) {
            return 0;
}
        else {
            return 1;
}
//-------------------------------------------------------------------------
}
```

## Explanation  

#### Solution 1  
- bypass the exception by using LegacyMergeSort instead of TimSort. [TimSort replaced LegacyMergeSort after JDK 7].
- When do use this solution? If you are sure that your Compare() method is correct, use this solution.  

#### Solution 2  
- usually when this exception occur, the comprare method may have not include all conditions.
- Since orignal code is  


```java

// Here is the original part.-----------------------------------------------
if (results1[0] < results2[0]) {return -2;}
else if (results1[0] == results2[0]) {
    if (results1[1] <= results2[1]) {
        return -1;
    } else {
return 0; }
}
else {
    return 1;
}

```


Since compare method only return three value: -1,0,1  

if you want `results1[1]<=results2[1] return -1`. My suggestion is to write this situation together. Thus,  

```java
if (results1[0] < results2[0] || results1[1]<=results2[1]) {
        return -1;
}
```  
After add this, we still need to consider results1[1]>results2[1] this situation. Thus,  
```java
else if (results1[0] == results2[0] || results1[1]>results2[1]) {
    return 0;
}
```

else return 1  

Finally, we include all three situation where comparing the list of reuslts1 and reuslts2. The main method should run without exception.  

## Reference  
java.util.TimSort  
Similar issue:
1. https://bugs.java.com/bugdatabase/view_bug.do?bug_id=7193557  
2. https://bugs.java.com/bugdatabase/view_bug.do?bug_id=8234482  

Comparator:  
1. https://www.scaler.com/topics/java/comparable-and-comparator-in-java/  
2. https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html  



