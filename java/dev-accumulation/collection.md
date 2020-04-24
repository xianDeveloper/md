# collection

## List

1. Java List按大小分片，平均切分

   写代码时有时需要将List按XX大小分片，或者均分成几个List，此时最好不要new很多新的List然后向里面add，这样做效率很低，下面介绍两种高效分片的方法。

   - 按固定大小分片

     ```java
     
     import com.google.common.collect.Lists;
     public class ListsPartitionTest {
         public static void main(String[] args) {
             List<String> ls = Arrays.asList("1,2,3,4,5,6,7,8,9,1,2,4,5,6,7,7,6,6,6,6,6,66".split(","));
             /*直接用guava库的partition方法即可*/
             System.out.println(Lists.partition(ls, 20));
             //按每50个一组分割
             List<List<String>> parts = Lists.partition(Lists, 50);
             parts.stream().forEach(list -> {
                 
             });
             /*直接用apache common collection4 ListsUtil 库的 partition 方法即可*/
             List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
     		List<List<Integer>> subs = ListUtils.partition(intList, 3);
         }
     }
     ```

     

   - 将List均分成n份

     ```java
        /**
          * 将一个list均分成n个list,主要通过偏移量来实现的
          *
          * @param source
          * @return
          */
         public static <T> List<List<T>> averageAssign(List<T> source, int n) {
             List<List<T>> result = new ArrayList<List<T>>();
             int remaider = source.size() % n;  //(先计算出余数)
             int number = source.size() / n;  //然后是商
             int offset = 0;//偏移量
             for (int i = 0; i < n; i++) {
                 List<T> value = null;
                 if (remaider > 0) {
                     value = source.subList(i * number + offset, (i + 1) * number + offset + 1);
                     remaider--;
                     offset++;
                 } else {
                     value = source.subList(i * number + offset, (i + 1) * number + offset);
                 }
                 result.add(value);
             }
             return result;
         }
         
         /**
          * 将一个list分成n个list, 
          *   每个list放的是商的个数，最后除数的余数放入最后一个list里
          *
          * @param source
          * @return
          */
         public static <T> List<List<T>> averageAssign(List<T> source, int n) {
             List<List<T>> result = new ArrayList<List<T>>();
             int number = source.size() / n;  //商
             for (int i = 0; i < n; i++) {
                 List<T> value = null;
                 if (i == n - 1) {
                     value = source.subList(i * number, source.size());
                 } else {
                     value = source.subList(i * number, (i + 1) * number);
                 }
                 result.add(value);
             }
             return result;
         }
     ```

   - java8 Stream 大数据量List分批处理

     ```java
     //按每3个一组分割
     private static final Integer MAX_NUMBER = 3;
     
     /**
     * 计算切分次数
     */
     private static Integer countStep(Integer size) {
     	return (size + MAX_NUMBER - 1) / MAX_NUMBER;
     }
     
     public static void main(String[] args) {
           List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
           int limit = countStep(list.size());
           //方法一：使用流遍历操作
           List<List<Integer>> mglist = new ArrayList<>();
           Stream.iterate(0, n -> n + 1).limit(limit).forEach(i -> {
               mglist.add(list.stream().skip(i * MAX_NUMBER).limit(MAX_NUMBER).collect(Collectors.toList()));
           });
     
           System.out.println(mglist);
     
           //方法二：获取分割后的集合
           List<List<Integer>> splitList = Stream.iterate(0, n -> n + 1).limit(limit).parallel().map(a -> list.stream().skip(a * MAX_NUMBER).limit(MAX_NUMBER).parallel().collect(Collectors.toList())).collect(Collectors.toList());
           
           System.out.println(splitList);
     }
     ```

     - 使用Java8 stream流 partition by partitioningBy是一种特殊的分组，只会分成两组

       ```java
        List<Integer> nums = Lists.newArrayList(1,1,8,2,3,4,5,6,7,9,10);
       Map<Boolean,List<Integer>> numMap= numList.stream().collect(Collectors.partitioningBy(num -> num > 5));
               System.out.println(numMap);
       {false=[2, 3, 4, 5], true=[8, 6, 7, 9, 10]}
       ```

       

2. 

