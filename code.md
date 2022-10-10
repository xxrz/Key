# 基础

排序

快排

```java
//快速排序
public static void QuickSort(int[] nums){
    if(nums==null || nums.length<2){
        return;
    }
    QuickSort(nums,0,nums.length-1);
}

public static void QuickSort(int[] nums,int L,int R){
    //返回值的条件，包含了>
    if(L>=R) {
        System.out.println(Arrays.toString(nums));
        return;
    }
    swap(nums,R,(int)(Math.random()*(R-L+1)+L));
    int less = partition(nums,L,R);
    QuickSort(nums,L,less);
    QuickSort(nums,less+1,R);
}

public static int partition(int[]nums, int L, int R){
    int pivot = nums[R];
    int index = L;
    int less = L-1;
    while(index < R){
        if(nums[index] < pivot){
            swap(nums,++less,index++);
        }else{
            index++;
        }
    }
    swap(nums,++less,R);
    return less;
}
```



堆排

```java
    public static void HeapSort(int[] arr, int k) {
        //维护K值的大根堆
        if(arr==null||arr.length < 2){
            return;
        }
        int heapsize = arr.length;
        buildHeap(arr,heapsize);
        //调整堆
        swap(arr,--heapsize,0);
        while(heapsize > 0){
            heapify(arr,0,heapsize);
            swap(arr,--heapsize,0);
        }
    }

    public static void buildHeap(int[] arr,int heapsize){
        for(int i= arr.length-1; i >=0 ; i--){
            heapify(arr,i,arr.length);
        }
    }

    public static void heapify(int[] arr,int i,int heapsize){
        int left = 2*i + 1,right = left + 1;
        int largest = i;

        while(left < heapsize){
            if(left < heapsize && arr[left] > arr[largest]){
                largest = left;
            }
            if(right < heapsize && arr[right] > arr[largest]){
                largest = right;
            }
            if(largest==i){
                break;
            }
            swap(arr,largest,i);
            i = largest;
            left = 2*i + 1;
            right = left + 1;
        }
    }
```



**KMP**

```java
    public int strStr(String haystack, String needle) {
        if(needle.isEmpty()) return 0;

        if (haystack == null ||  needle.length() < 1 || haystack.length() < needle.length()) {
            return -1;
        }
        

        char[] h = haystack.toCharArray(); //母串
        char[] n = needle.toCharArray(); //子串

        int x = 0; //母串下标
        int y = 0; //子串下标
        
        //O(N)
        int[] next = getNext(n);
        while(x < h.length && y < n.length){
            if(h[x] == n[y]){
                x++;
                y++;
            }else if(next[y]==-1){  //y = 0;
                x++; 
            }else{
                y = next[y];
            }
        }

        return y==n.length? x-y : -1;

    }

    //只和子串有关
    public int[] getNext(char[] n){
        if(n.length==1) return new int[]{-1};

        int[] next = new int[n.length];
        next[0] = -1;
        next[1] = 0;
        int index = 2;
        int cn = 0;  //当前需要比较的位置，同时也是值
        while(index < n.length){
            if(n[index-1] == n[cn]){   //注意此处是index-1,因为前一个就不包含他本身
                next[index++] = ++cn;  //此处是++cn
            }else if(cn > 0){  //为什么没有等  cn=0的时候是-1
                cn = next[cn];
            }else{
                next[index++] = 0;  //没有前后缀是0
            }
        }
        
        return next;
    }
```



**二叉树**

二叉树中序非递归

```java
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new LinkedList<>();

        if(root==null) return res;

        LinkedList<TreeNode> s = new LinkedList<>();
        TreeNode cur = root;

        while(!s.isEmpty() || cur!=null){
            while(cur!=null){
                s.addLast(cur);
                cur = cur.left;
            }
            TreeNode node = s.removeLast();
            res.add(node.val);
            cur = node.right;
        }

        return res;
    }
```



建树

```java
import java.util.*;

public class test {

    static class TreeNode{
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int val){
            this.val = val;
        }
    }

    static TreeNode head;
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int cnt = scanner.nextInt();
        LinkedList<Integer> nums = new LinkedList<>();
        for(int i = 0;i < cnt;i++){
            nums.addLast(scanner.nextInt());
        }

        System.out.println(nums);
        head = buildTree(nums);
        preOrder(head);
    }

    public static void preOrder(TreeNode head){
        if(head==null) return;

        System.out.println(head.val);
        preOrder(head.left);
        preOrder(head.right);
    }

    private static TreeNode buildTree(LinkedList<Integer> nums) {
        if(nums.size()==0) return head;

        int val = nums.removeFirst();
        if(val==-1) return null;

        TreeNode root = new TreeNode(val);
        root.left = buildTree(nums);
        root.right = buildTree(nums);

        return root;
    }
}
```



**链表**

删除所有重复节点

```java
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;

        ListNode cur = head,pre = dummy;
        while(cur!=null){
            while(cur!=null && cur.next!=null && cur.val==cur.next.val){
                cur = cur.next;
            }
            if(pre.next == cur){
                pre = cur;
            }else{
                pre.next = cur.next;
            }
            cur = cur.next;
        }

        return dummy.next;
    }
```



# **数据结构**

LRU：删除队首，队尾元素是最新的

```java
class LRUCache {
    LinkedHashMap<Integer,Integer> map;
    int cap;

    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new LinkedHashMap<>();
    }
    
    public int get(int key) {
        if(!map.containsKey(key)) return -1;

        int value = map.get(key);
        // System.out.println("key:"+key+"\tvalue:"+value);
        map.remove(key);
        map.put(key,value);
        return value;
    }
    
    public void put(int key, int value) {
        // System.out.println("map.size():"+map.size()+"\tcap:"+cap);
        if(map.containsKey(key)) map.remove(key);
        if(map.size()==cap) map.remove(map.keySet().iterator().next());

        map.put(key,value);
    }
}
```



前缀树

```java
class Trie {
    private class Node{
        int pass;
        int end;
        Node[] nexts; 

        Node(){
            pass = 0;
            end = 0;
            nexts = new Node[26];
        }
    }

    Node root;

    public Trie() {
        root = new Node();    
    }
    
    public void insert(String word) {
        if(word==null) return;

        char[] str = word.toCharArray();
        Node node = root;
        node.pass++;

        for(int i = 0;i < str.length;i++){
            int index = str[i]-'a';
            if(node.nexts[index] == null){
                node.nexts[index] = new Node();
            }
            node = node.nexts[index];
            node.pass++;
        }
        node.end++;
    }
    
    public boolean search(String word) {
        if(word==null) return true;

        char[] str = word.toCharArray();
        Node node = root;
        for(int i = 0;i < str.length;i++){
            int index = str[i]-'a';
            if(node.nexts[index]==null){
                return false;
            }
            node = node.nexts[index];
        }
        return node.end > 0? true:false;
    }
    
    public boolean startsWith(String prefix) {
        if(prefix==null) return true;

        char[] str = prefix.toCharArray();
        Node node = root;
        for(int i = 0;i < str.length;i++){
            int index = str[i]-'a';
            if(node.nexts[index]==null){
                return false;
            }
            node = node.nexts[index];
        }
        return true;
    }
}
```



并查集

```java
class UF{
    HashMap<Integer,Integer> father;

    UF(){
        father = new HashMap<>();
    }

    void add(int x){
        if(father.containsKey(x))
            father.put(x,null);
    }

    void merge(int x,int y){
        int r1 = find(x);
        int r2 = find(y);

        if(r1!=r2){
            father.put(r1,r2);
        }
    }

    int find(int x){
        int root = x;
        while(father.get(root)!=null){
            root = father.get(root);
        }

        while(father.get(x)!=null){
            int tmp = father.get(x);
            father.put(x,root);
            x = tmp;
        }

        return root;
    }

    boolean isConnected(int x,int y){
        int r1 = find(x);
        int r2 = find(y);

        return r1==r2;
    }

}
```



# 图

最短路径，迪杰斯特拉算法

```java
package com.zhou.algorithm;
import java.util.Scanner;
 
/**
 *
 * 迪杰斯特拉算法:
 * 思路：选取一个顶点作为起点，刚开始顶点集合中只有起始点（源点）一个，
 * 然后通过查找到源点距离最短的顶点，将这个顶点加入到顶点集合中，然后
 * 更新加入这个顶点之后源点到其他顶点的距离，一步步把所有的顶点都加入到
 * 顶点集合中，等到顶点全部加入到顶点集合中去，顶点集合的顺序便是从源点出发遍历所有顶点的最短路径。
 */
public class Dijkstra {
    public static int[] dijkstra(int[][] distance,int n,int x){
        //记录最短路径顺序的数组
        int[] path = new int[n];
        //最短路径元素的下标
        int pathIndex = 0;
        //先将源点放到最短路径顺序数组中
        path[pathIndex++] = x;
        //d数组存储的是其余的点到源点的距离
        int[] d = new int[n];
        //代表是否加入顶点集合中,false代表未加入，true代表已经加入了顶点集合中。
        boolean[] flag = new boolean[n];
        //刚开始顶点集合中只有源点
        flag[x-1] = true;
        //初始化
        for (int i = 0; i < n; i++) {
            d[i] = distance[x-1][i];
        }
        //index为要新加入顶点集合的下标
        int index = x-1;
        //循环n-1次，将剩下的n-1个顶点加入到顶点集合中
        for (int count = 0;count<n-1;count++){
            int temp = MAX_INT;
            //找出到源点最小距离的点
            for (int i = 0; i < n; i++) {
                //当这个顶点为未加入顶点集合并且距离小于临时值时
                if ((!flag[i])&&d[i]<temp){
                    temp = d[i];
                    index = i;
                }
            }
            path[pathIndex] = index+1;
            pathIndex++;
            //将顶点加入到顶点集合中去
            flag[index] = true;
            //添加节点之后，更新未加入顶点集合的顶点到源点的距离
            for (int i = 0; i < n; i++) {
                if(!flag[i]&&distance[index][i]<MAX_INT){
                    int change = d[index]+distance[index][i];
                    if(change<d[i]){
                        d[i] = change;
                    }
                }
            }
        }
        return path;
    }
    //定义一个常量，如果两个顶点之间没有通路，那么两点间的距离为MAX_INT;
    public final static int MAX_INT = 10000;
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入一共有几个顶点");
        int n = scanner.nextInt();
        System.out.println("源节点是哪个:");
        int x = scanner.nextInt();
        //存储的是所有的节点之间的距离，distance[i][j]表示的是从顶点i到顶点j的距离，如果没有通路则为MAX_INT,distance[i][i]为0
        int[][] distance={
                {0, 3, 6, MAX_INT, MAX_INT},
                {3, 0, 4, MAX_INT, MAX_INT},
                {MAX_INT, 4, 0, 8, MAX_INT},
                {MAX_INT, MAX_INT, 8, 0, 3},
                {MAX_INT, MAX_INT, MAX_INT, 3, 0}
        };
        int[] path = dijkstra(distance,n,x);
        for (int i = 0; i < path.length; i++) {
            if (i== path.length-1){
                System.out.print(path[i]);
            }else{
                System.out.print((path[i])+"---->");
 
            }
        }
    }
}
```

```java
public class FloydAlgorithm {
    public static int MaxValue = 100000;
    public static int[][] path;
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        System.out.println("请输入顶点数和边数:");
        //顶点数
        int vertex = input.nextInt();
        //边数
        int edge = input.nextInt();
 
        int[][] matrix = new int[vertex][vertex];
        //初始化邻接矩阵
        for (int i = 0; i < vertex; i++) {
            for (int j = 0; j < vertex; j++) {
                matrix[i][j] = MaxValue;
            }
        }
 
        //初始化路径数组
        path = new int[matrix.length][matrix.length];
 
        //初始化边权值
        for (int i = 0; i < edge; i++) {
            System.out.println("请输入第" + (i + 1) + "条边与其权值:");
            int source = input.nextInt();
            int target = input.nextInt();
            int weight = input.nextInt();
            matrix[source][target] = weight;
        }
 
        //调用算法计算最短路径
        floyd(matrix);
    }
 
    //非递归实现
    public static void floyd(int[][] matrix) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix.length; j++) {
                path[i][j] = -1;
             }
        }
 
        for (int m = 0; m < matrix.length; m++) {
            for (int i = 0; i < matrix.length; i++) {
                for (int j = 0; j < matrix.length; j++) {
                    if (matrix[i][m] + matrix[m][j] < matrix[i][j]) {
                        matrix[i][j] = matrix[i][m] + matrix[m][j];
                        //记录经由哪个点到达
                        path[i][j] = m;
                    }
                }
            }
        }
 
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix.length; j++) {
                if (i != j) {
                    if (matrix[i][j] == MaxValue) {
                        System.out.println(i + "到" + j + "不可达");
                    } else {
                        System.out.print(i + "到" + j + "的最短路径长度是：" + matrix[i][j]);
                        System.out.print("最短路径为：" + i + "->");
                        findPath(i, j);
                        System.out.println(j);
                    }
                }
            }
        }
    }
 
    //递归寻找路径
    public static void findPath(int i, int j) {
        int m = path[i][j];
        if (m == -1) {
            return;
        }
 
        findPath(i, m);
        System.out.print(m + "->");
        findPath(m, j);
    }
}
```

拓扑排序

```java
/两个节点不重复，这么做会减少计算量，如果碰到visited被标记为true，则说明前面已经对这个节点之后的样子做出判断了，无须再进行延伸，所以减少计算量
    boolean[] visited;//（代表以这个节点为头的路径是否有判断过）
    boolean[] onPath; //记录当前路径上存在的节点（只代表一条路径）
    //有环就是当前路径上存在两个一样的节点
    // 记录后序遍历结果
    List<Integer> postorder = new ArrayList<>();

    // 记录图中是否有环
    boolean hasCycle = false;

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<Integer>[] graph = buildGraph(numCourses,prerequisites);

        visited = new boolean[numCourses];
        onPath = new boolean[numCourses];

        //因为图不是连通的，所以需要遍历每个节点
        for (int i = 0; i < numCourses; i++) {
            // 遍历图中的所有节点
            traverse(graph, i);
        }

        if(hasCycle){
            return new int[]{};
        }

        Collections.reverse(postorder);
        int[] res = new int[numCourses];
        for(int i=0;i < numCourses;i++){
            res[i] = postorder.get(i);
        }

        return res;
    }

    void traverse(List<Integer>[] graph, int i){
        //base case
        if(onPath[i]==true) {
            hasCycle = true;
            return;
        }

        if(visited[i]==true) return;
        
        //先序代码的位置
        visited[i] = true;
        onPath[i] = true;
        //标志位的判断也要放在先序，准确得说是该放哪放哪，只是打印的语句有所变化
        for(int v : graph[i]){
            traverse(graph,v);
        }

        //后序代码的位置
        //加入当前节点，邻接表，也就是i
        postorder.add(i);
        onPath[i] = false;
    }

    List<Integer>[] buildGraph(int numCourses, int[][] prerequisites){
        //图中共有numCourses个节点
        //二维数组，采用邻接表的方式表示图
        List<Integer>[] graph = new LinkedList[numCourses];
        
        for(int i = 0;i < numCourses;i++){
            //graph[i]中放得i节点到其他的节点的节点列表
            graph[i] = new LinkedList<>();
        }

        for(int[] edge:prerequisites){
            int from= edge[1],to = edge[0];
            // 添加一条从 from 指向 to 的有向边
            // 边的方向是「被依赖」关系，即修完课程 from 才能修课程 to
            graph[from].add(to);
        }

        return graph;
    }
```

二分图

```java
   boolean[] visited;
    boolean[] color;
    int m;
    boolean res = true;
    public boolean isBipartite(int[][] graph) {
        m = graph.length;
        visited = new boolean[m];
        color = new boolean[m];
    
        for(int v = 0;v < m;v++){
            //visited的判断放在这是因为，travel针对visited过的情况还需要处理，还不会直接return
            if(!visited[v])
                dfs(graph,v);
        }

        return res;
    }

    public void dfs(int[][] graph,int v){
        if(!res) return;

        visited[v] = true;

        for(int nb:graph[v]){
            //没有被访问过
            if(!visited[nb]){
                color[nb] = !color[v];
                dfs(graph,nb);
            //被访问过
            }else{
                if(color[nb]==color[v]) res = false;
            }
        }
    }
```



# **API**

比较器

```java
PriorityQueue<ListNode> heap = new PriorityQueue<>(new Comparator<ListNode>(){
            public int compare(ListNode node1,ListNode node2){
                return node1.val-node2.val;
            }
        });
```

```java
Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1); // 逆序
    }
});
```

```java
int[][] a = new int[4][2];
        Arrays.sort(a, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return 0;
            }
        });
//
Arrays.sort(a,(x1,x2)->{
    return x1-x2;
});
```

```java
o1.compareTo(o2); //快速字符串对比
```

优先队列

```java
PriorityQueue<ListNode> heap;
heap.offer(val);
head.poll();
```

数组返回最大值

```java
Arrays.stream(dp).max().getAsInt();
```

反转

```java
//集合
Collections.reverse(tmp);
//stingbuilder
str.reverse();
```

四舍五入问题

```java
double x = 3.55;
//java保留小数点后一位，不四舍五入
Math.floor(x*10d)/10;
//向下取整,返回值是double类型
Math.floor(x); 
//四舍五入
Math.round(x);
//向上取整
Math.ceil(x);
//超过一半
(a+1)/2;
```

Hashmap操作

```java
HashMap<String,Integer> map = new HashMap<>();
//获取Entry的集合
Set<Map.Entry<String, Integer>> entries = map.entrySet();
//获取key的集合
Set<String> strings = map.keySet();
 //获取值的集合
Collection<Integer> values = map.values();
//这个遍历效率高
Iterator<String> iterator = map.keySet().iterator();
while(iterator.hasNext()){
   System.out.println(iterator.next());
}
//其他的api
map.getOrDefault("srt",1);
```

ArrayDeque

```java
Deque<int[]> res = new ArrayDeque<>();
res.offerFirst(e);
res.addFirst(e);
res.pollFirst();
res.getFirst();
```



# **设计模式**

## 单例

```java
/*
饿汉式：
1.定义一个私有的构造方法
2.定义一个类变量并实例化
3.定义一个可以获取单例的方法
 */
public class Single {

    private static Single single = new Single();

    private Single(){}

    private Single getSingle(){
        return single;
    }

}
```

```java
/*
懒汉式；
1. 创建一个votaile的全局的类变量
2. 私有化构造函数
3. 创建对象的方法
    双重校验锁

 */
class LazySingle {
    //为什么是volatile：如果存在多线程，则指令重拍可能会导致出错
    private static volatile LazySingle lazySingle;

    private LazySingle(){}

    public static LazySingle getLazySingle(){
        if(lazySingle!=null) return lazySingle;

        //如果没有对象，则创建
        //为什么要加锁：保证只有一个线程在获取ExerciseLazySingle类的资源
        synchronized (LazySingle.class){
            if(lazySingle==null){
                //为什么要加判断：如果存在线程竞争，时间片轮转，则会导致可能exerciseLazySingle不为空
                lazySingle = new LazySingle();
            }
        }

        return lazySingle;
    }

}
```



## 观察者

定义观察者接口

```java
public interface Observer {
    public void update(float temperature, float pressure, float humidity);
}
```

定义主体接口

```java
public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObserver();
}
```

实现一个主体

```java
public class WeatherData implements Subject{
    private float temperature;
    private float pressure;
    private float humidity;
    private ArrayList<Observer> observers;
 
    public WeatherData() {
       observers = new ArrayList<>();
    }
 
    public float getTemperature() {
        return temperature;
    }
 
 
    public float getPressure() {
        return pressure;
    }
 
 
    public float getHumidity() {
        return humidity;
    }
 
    //更新
    public void dataChange(){
        notifyObserver();
    }
    
    //当数据有更新时调用
    public void setData(float temperature,float pressure,float humidity){
        this.temperature = temperature;
        this.pressure = pressure;
        this.humidity = humidity;
        //将最新的信息推送给第三方
        dataChange();
    }
    //注册一个观察者
    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }
    //移除一个观察者
    @Override
    public void removeObserver(Observer o) {
        if(observers.contains(o)) {
            observers.remove(o);
        }
    }
 //通知所有观察者
    @Override
    public void notifyObserver() {
        for (int i = 0; i < observers.size(); i++) {
            observers.get(i).update(this.temperature,this.pressure,this.humidity);
        }
    }
}
```

观察者：更新

```java
public class CurrentConditions implements Observer{
    private float temperature;
    private float pressure;
    private float humidity;
 
    //更新，第三方调用，推送
    public void update(float temperature, float pressure, float humidity) {
        this.temperature = temperature;
        this.pressure = pressure;
        this.humidity = humidity;
        display();
    }
 
    public void display() {
        System.out.println("***********气象台************");
        System.out.println("今天的气温 " + temperature);
        System.out.println("今天的气压 " + pressure);
        System.out.println("今天的湿度 " + humidity);
    }
}
```



# **数据库基本命令**

```sql
增：insert into +表 values（value1，value2...）
删：delect from +表 where 值
改：update +表 set 字段=新值 where (也可以加from 用于join)
查：select * from +表 where ....
like ：select * from +表 where 值 like '%字段%'
in：允许在where中规定多个值 select * from + 表 where 字段 in（value1，value2...）
between：select * from +表 where 字段 between 最小值 And 最大值
inner join：select * from +表1 inner join 表2 on 表1.id =表2.id
left join 与right join：
left join 是罗列出左表中的所有数据，然后找出右表中满足条件的所有数据，右表数据不足的地方用NULL填充
right join 是罗列出右表中的所有数据，然后找出左表中满足条件的所有数据，左表数据不足的地方用NULL填充
聚合函数不能放在where子句中
平均值avg()：select avg(字段) from 表 where +条件
求行数量count()：select count(字段) from +表 where +条件
累计之和sum()：select sum(字段) from +表
最大值max()：select max(字段) from +表
最小值min()：select min(字段) from +表

-- result和result的类型要一样
-- 也可以使用子查询
CASE WHEN condition THEN result
[WHEN...THEN...]
ELSE result 
END


select dname,avg(salary) 
from emp inner join dept 
where emp.id=dept.id And salary >=6000 
group by dname

update emp set salary=salary*1.1 
from emp A,eno B 
where A.dno=B.dno And B.dname ='销售部'And B.salary<6000；
```



```sql
create database school; -- 创建数据库
use school; -- 使用该数据库
show tables; -- 显示表
drop database school; -- 删除数据库
create table school( -- 创建表
id int not null auto_increment,
    name varchar(100) not null,
    lasttime int,
    primary key(id)
);
insert into school (name,lasttime) values ("reno",'20160826');-- 插入数据
select * from school where id = 1; -- 查询数据
update school set name="new" where id = 2; -- 更新数据
delete from school where name="new"; -- 删除数据
alter table school add index/key idx(`new`);
alter table school add unique (`new`);
alter table school add primary key(`name`);
ALTER TABLE staffs ADD sex CHAR DEFAULT '女'; 

ifNull(x,null) -- 如果为空则返回空
datediff(x,y) -- x,y之间差得天数
-- 可以通过自连接进行排序和对比，多个自己
SELECT a.Sno FROM (SELECT Sno,score FROM SC WHERE Cno='001') a, -- 临时表
...where uid in( -- 子查询常用in
select uid from...)
-- having中的字段必须出现在select中,聚合函数除外
-- join tablename on 连接相同的字段
-- 巧用distinct
-- 必要时反过来想问题，然后取反
```



划分等级

```sql
SELECT COUNT(id),
(
CASE WHEN age < 25 THEN 'A'
WHEN age < 35 AND age>=25 THEN 'B'
ELSE 'C' END
) AS LEVEL
FROM staffs
GROUP BY LEVEL
ORDER BY LEVEL
```



一个表统计人数

```sql
SELECT 
SUM(CASE WHEN sex='男' THEN 1 ELSE 0 END) AS male_count,
SUM(CASE WHEN sex='女' THEN 1 ELSE 0 END) AS female_count,
SUM(CASE WHEN sex='男' AND age>50 THEN 1 ELSE 0 END) AS male_pass,
SUM(CASE WHEN sex='女' AND age<=50 THEN 1 ELSE 0 END) AS female_pass
FROM staffs
```



统计每个分类的电的和

注意：

group by分组，表示的是行

case when表示列，

```sql
SELECT e_code,
SUM(CASE WHEN e_type = 0 THEN e_value ELSE 0 END) AS water,
SUM(CASE WHEN e_type = 1 THEN e_value ELSE 0 END) AS ele,
SUM(CASE WHEN e_type = 2 THEN e_value ELSE 0 END) AS heat
FROM t
GROUP BY e_code 
```



不连续的记录

```sql
 -- 查询表A中第i至j条记录,id为主键，但不是连续增长的，但是是增长的
 -- 找出0-i-1条记录中的id的最大值max(id)
 -- 以后的id > max(id)的记录要(j-i+1)条
 
 -- example 查找4-6的记录，表和结果如下
 SELECT *
 FROM A
 WHERE id > 
 (
 	SELECT MAX(id)
 	FROM (
 		SELECT id 
 		FROM A
 		ORDER BY id
 		LIMIT 0,3
 	) AS t
 )
 ORDER BY id
 LIMIT 0,3
```



名次

```sql
SELECT 1 + COUNT(t1.平均成绩)
FROM (
	SELECT Sid,AVG(score) 平均成绩 
	FROM SC 
	GROUP BY Sid ) t1,
	(
	SELECT Sid,AVG(score) 平均成绩 
	FROM SC 
	GROUP BY Sid ) t2
WHERE t1.平均成绩 > t2.平均成绩
ORDER BY t2.平均成绩 DESC;
```



排名

```sql
-- a是当前的表，以a为基准，也就是笛卡尔乘积里不变的那个,结果里要的是a, 用b去看名次
SELECT a.Sno,a.Cno,a.`score` FROM SC AS a 
LEFT JOIN SC b ON a.Cno = b.`Cno` AND a.score < b.score
GROUP BY a.Cno,a.Sno,a.Score
HAVING COUNT(b.Sno) < 2
ORDER BY a.Cno,a.Score DESC

b  a
x  99
y  98
------
z  97

-- 如果算97的名次，只有b== (x)99,(y)98时，才能让COUNT(b.Sno) < 2
```



# 多线程

## 打印9为0

方法一：

```java
    private int cnt = 9;
    @Override
    public void run() {
        while(true){
            //要考虑加锁
            synchronized (ExerciseCDL.class){
                if(cnt==0){
                    System.out.println("数字为0，结束递减");
                    break;
                }
                cnt--;
                System.out.println(Thread.currentThread().getName()+":cnt:"+cnt);
            }

        }
    }

    public static void main(String[] args) {
        //设置初始值为子线程的数量
        ExerciseCDL MyThread = new ExerciseCDL();
        Thread thread1 = new Thread(MyThread);
        Thread thread2 = new Thread(MyThread);
        Thread thread3 = new Thread(MyThread);
        thread1.start();
        thread2.start();
        thread3.start();
    }
```

这样的话，会打印三次，每个线程都在打印

方法二：巧用join，主线程等子线程运行完

```java
    private int cnt = 9;
    @Override
    public void run() {
        while(true){
            //要考虑加锁
            synchronized (ExerciseCDL.class){
                if(cnt==0){
                    break;
                }
                cnt--;
                System.out.println(Thread.currentThread().getName()+":cnt:"+cnt);
            }

        }
    }

    public static void main(String[] args) throws InterruptedException {
        //设置初始值为子线程的数量
        ExerciseCDL MyThread = new ExerciseCDL();
        Thread thread1 = new Thread(MyThread);
        Thread thread2 = new Thread(MyThread);
        Thread thread3 = new Thread(MyThread);
        thread1.start();
        thread2.start();
        thread3.start();
        //主线程等三个线程都执行完，才开始写数字
        thread1.join(); //主线程等待t1线程结束
        thread2.join();//主线程等待t2线程结束
        thread3.join();//主线程等待t3线程结束
        //其他不要ran,t1不等t2之类的
        System.out.println("数字为0，结束递减");
    }
```

方法三：

```java
public static void main(String[] args) throws InterruptedException {
        //设置初始值为子线程的数量
        ExerciseCDL MyThread = new ExerciseCDL();
        Thread thread1 = new Thread(MyThread);
        Thread thread2 = new Thread(MyThread);
        Thread thread3 = new Thread(MyThread);
        thread1.start();
        thread2.start();
        thread3.start();
		
    	//设置标志，注意必须使用for循环，不能直接if判断，因为主线程可能会先执行
        while(true){
            if(MyThread.cnt==0){
                break;
            }
        }

        System.out.println("数字为0，结束递减");
```

方法四：countDownLatch+线程池

```java
import java.util.concurrent.*;

public class T4{
    private static int var = 9;

    public static void main(String[] args) throws InterruptedException {
        //倒数的计数器,不是放线程的数量
        CountDownLatch countDownLatch = new CountDownLatch(var);
        //创建线程池
        ExecutorService executorService = Executors.newFixedThreadPool(3);
        for(int i = 0;i < 9;i++){
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    //加锁保证数据安全
                    synchronized (T4.class) {
                        var--;
                        System.out.println(Thread.currentThread().getName()+":"+var);
                        countDownLatch.countDown();
                    }
                }
            });
        }
        countDownLatch.await();
        System.out.println("over");
        executorService.shutdown();
    }
}
```



## 信号量

### 生产者消费者

比较简单和基础，只是如果生产者进去没有empty会睡眠等待消费者消费，但如果已经加了mutex，消费者无法进入，就会死锁



临界资源：缓冲区

信号量：

空缓冲区的数量

满缓冲区的数量



定义互斥量mutex

```java
//生产者
wait(empty);
wait(mutex);
product();
signal(mutex);
signal(full);

//消费者
wait(full);
wait(mutex);
consume();
signal(mutex);
signal(empty);
```



### 哲学家进餐问题

[参考](https://blog.csdn.net/qq_28602957/article/details/53538329)
[参考](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/Java%20%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%2078%20%E8%AE%B2-%E5%AE%8C/71%20%E8%AE%B2%E4%B8%80%E8%AE%B2%E7%BB%8F%E5%85%B8%E7%9A%84%E5%93%B2%E5%AD%A6%E5%AE%B6%E5%B0%B1%E9%A4%90%E9%97%AE%E9%A2%98.md)

五个哲学家围着一张圆桌，每个哲学家面前放着食物。哲学家的生活有两种交替活动：吃饭以及思考。当一个哲学家吃饭时，需要先拿起自己左右两边的两根筷子，并且一次只能拿起一根筷子。

>   **遇到这个问题，要先明白，什么是临界资源，要怎么处理临界资源**
>
>   **谁会操作临界资源**
>
>   比如这个题里：五根筷子是临界资源
>
>   
>
>   注意：如果所有哲学家同时拿起左手边的筷子，那么所有哲学家都在等待其它哲学家吃完并释放自己手中的筷子，导致死锁。考虑一下三种方案

#### synchronized

如果不强调吞吐量，尽量把锁颗粒度放大，因为简单好实现。

```java
class DiningPhilosophers {

    public DiningPhilosophers() {
        
        
    }

    // call the run() method of any runnable to execute its code
    public synchronized void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {
                               pickLeftFork.run();
                               pickRightFork.run();
                               eat.run();
                               putLeftFork.run();
                               putRightFork.run();
        
    }
}
```

#### 方法一：

只允许四个人同时拿起筷子

```java
//可以利用reentrantLock去实现
semaphore ch[5]  = {1,1,1,1,1};
//可以用semaphore去实现
semaphore limit = 4;

//传入参数：第几位哲学家
void philosopher(int i){
    while(true){
    	think();
    	wait(limit);
    	wait(ch[i]);
    	wait(ch[(i+1)%5]);
    	eat();//可以利用runnable的线程去实现
    	signal(ch[(i+1)%5]);
    	signal(ch[i]);
    	signal(limit);
    }
	
}
```

reentrantlock

对筷子的访问，进行上锁，

同时控制信号量

```java
class DiningPhilosophers {

 //控制对筷子的访问！拿起和放下
    private final ReentrantLock[] lockList = {
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
    };

    //定义信号量：限制最多只有4个哲学家持有筷子
    private Semaphore limit = new Semaphore(4);


    public void DiningPhilosophers(){

    }


    // call the run() method of any runnable to execute its code
    public void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {
        int left = philosopher;
        int right = (philosopher + 1)%5;

        limit.acquire();
        lockList[left].lock();
        lockList[right].lock();
        pickLeftFork.run();
        pickRightFork.run();
        eat.run();
        
        putLeftFork.run();
        putRightFork.run();
        lockList[left].unlock();
        lockList[right].unlock();
        limit.release();
    }
}
```

#### 方法二：

有一个人先拿起左右筷子，剩下的人随便（解决了死锁，但是可能会引起饥饿）

```java
semaphore ch[5]  = {1,1,1,1,1};
semaphore mutex = 1;

//传入参数：第几位哲学家
void philosopher(int i){
    while(true){
    	think();
    	wait(mutex);
    	wait(ch[i]);
    	wait(ch[(i+1)%5]);
    	signal(mutex);
    	eat();
    	signal(ch[(i+1)%5]);
    	signal(ch[i]);
    }
}
```

方法二：保证一个哲学家手上有筷子

```java
class DiningPhilosophers {

 //控制对筷子的访问！拿起和放下
    private final ReentrantLock[] lockList = {
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
    };

    //互斥量：让一个哲学家同时拿左右两边的筷子
    private Semaphore mutex = new Semaphore(1);
    //当然也可以做成临界区
    //  private ReentrantLock pickBothForks = new ReentrantLock();


    public void DiningPhilosophers(){

    }


    // call the run() method of any runnable to execute its code
    public void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {
        int left = philosopher;
        int right = (philosopher + 1)%5;

        mutex.acquire();
        lockList[left].lock();
        lockList[right].lock();
        pickLeftFork.run();
        pickRightFork.run();
        mutex.release();
        

        eat.run();

        putLeftFork.run();
        putRightFork.run();
        lockList[left].unlock();
        lockList[right].unlock(); 
    }
}
```

#### 方法三：

五个人中，只要有四个人同时先拿左边筷子，一个人先拿右边筷子，则一定有一个人会拿两个筷子，就不会造成死锁了。所以可以奇数哲学家先拿左筷子，偶数哲学家先拿右筷子。

或者随机选一个人用改变顺序，就可以防止饿死了！！！！

```java
semaphore ch[5]  = {1,1,1,1,1};
semaphore mutex = 1;

//传入参数：第几位哲学家
void philosopher(int i){
    while(true){
        think();
        if(i%2==0){
            wait(ch[(i+1)%5]);
			wait(ch[i]);
            eat();
            signal(ch[i]);
            signal(ch[(i+1)%5]);
        }else{
            wait(ch[i]);
            wait(ch[(i+1)%5]);
            eat();
            signal(ch[(i+1)%5]);
    		signal(ch[i]);
        }
    }
}
```

奇偶分开

```java
class DiningPhilosophers {

 //控制对筷子的访问！拿起和放下
    private final ReentrantLock[] lockList = {
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
            new ReentrantLock(),
    };

    public void DiningPhilosophers(){

    }


    // call the run() method of any runnable to execute its code
    public void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {
        int left = philosopher;
        int right = (philosopher + 1)%5;

        if(philosopher%2==0){
            lockList[left].lock();
            lockList[right].lock();
            pickLeftFork.run();
            pickRightFork.run();
            eat.run();
            putRightFork.run();
            putLeftFork.run();
            lockList[right].unlock();
            lockList[left].unlock();

        }else{
            lockList[right].lock();
            lockList[left].lock();
            pickRightFork.run();
            pickLeftFork.run();
            eat.run();
            putLeftFork.run();
            putRightFork.run();
            lockList[left].unlock();
            lockList[right].unlock();
        }
    }
}
```



### 读者写者问题

临界资源：数据

控制对数据的互斥访问：wrt，只需要看有没有在写，如果有则不行，如果没有就可以读;反过来想读者优先是如果没有在读者才可以写

信号量：

要读数据的线程：readcount

控制互斥更新读者的数量：mutex

```java
int readcount=0; 
semaphore mutex=1, wrt=1 ; 

Reader():
wait(mutex);
readcount++;
if(cnt==1) 
    wait(wrt);
signal(mutex);

read();

wait(mutex);
readcount--;
if(readcount==0)
    signal(wrt);
signal(mutex);

Writer():
wait(wrt);
writer();
signal(wrt);
```



## 死锁

```java
public class T5{
    //两个资源
    static final Object resource1 = new Object();
    static final Object resource2 = new Object();

    public static void main(String[] args) {

        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                synchronized (resource1){
                    try {
                        System.out.println("t1:get resource1");
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (resource2){
                        System.out.println("t1:get resource2");
                    }
                }

            }
        };

        Runnable r2 = new Runnable() {
            @Override
            public void run() {
                synchronized (resource2){
                    try {
                        System.out.println("t2:get resource2");
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (resource1){
                        System.out.println("t2:get resource1");
                    }
                }

            }
        };

        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();
        System.out.println("t1 start");
        t2.start();
        System.out.println("t2 start");
    }

}
```

解决死锁

```java
//解决死锁
/*
1.避免多次锁定。尽量避免同一个线程对多个 Lock 进行锁定。
2.具有相同的加锁顺序。
3.使用定时锁。程序在调用 acquire() 方法加锁时可指定 timeout 参数
4.同时分配多个资源，renntarntlock，绑定，破坏占有且等待
 */
public class T5{
    //两个资源
    static ReentrantLock resource1 = new ReentrantLock();
    static ReentrantLock resource2 = new ReentrantLock();

    public static void main(String[] args) {

        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                resource1.lock();
                System.out.println("t1:get resource1");
                resource2.lock();
                System.out.println("t1:get resource2");
                resource1.unlock();
                resource2.unlock();
            }
        };

        Runnable r2 = new Runnable() {
            @Override
            public void run() {
                resource2.lock();
                System.out.println("t2:get resource2");
                resource1.lock();
                System.out.println("t2:get resource1");
                resource1.unlock();
                resource2.unlock();
            }
        };

        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();
        System.out.println("t1 start");
        t2.start();
        System.out.println("t2 start");
    }

}
```



