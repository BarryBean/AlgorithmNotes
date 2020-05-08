[LRU 最近最少使用算法](https://baike.baidu.com/item/LRU)

# 设计
根据需求总结设计。
1. put、get的时间复杂度为O(1) => 哈希表，查找快
2. 内部有顺序性 => 链表，内部有顺序性
 

所以 LRU，需要使用**哈希表 + 双向链表**，能够快速查找结点且能快速删除添加。


注：Java 中的 LinkedHashMap 就是封装好的数据结构。


# 思路

1. 实现双向链表的结点类 Node。
2. 构造双向链表，提供头尾的虚结点，提供添加、删除、删除最后一个节点的方法。
3. 实现LRU。
    - get() 先判断 key 是否存在，存在就返回 val，并把这个节点调到开头。`这样使得最少使用的就是 last one。`
    - put() 先判断 key 是否存在，存在就覆盖，不存在且有空间就加入，没空间就置换腾位置。


```java
// 哈希表存节点值
HashMap<Integer, Node> map;
// 双向链表
DoubleList cache;

int get(int key) {
    if (key 不存在) {
        return -1;
    } else {        
        将数据 (key, val) 提到开头；
        return val;
    }
}

void put(int key, int val) {
    Node x = new Node(key, val);
    if (key 已存在) {
        删除旧数据；
        将新节点 x 插入到开头；
    } else {
        if (cache 已满) {
            删除最后一个数据；
            删除 map 中的映射；
        } 
        将新节点 x 插入到开头；
        map 中新建 key 的映射；
    }
}
```


# 代码模板


## 自实现
```java
    class Node {
        int key, val;
        Node next, pre;

        public Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }

    class DoubleList {
        //头尾的虚拟节点
        private Node head, tail;
        //记录节点数
        private int size;

        public DoubleList() {
            head = new Node(0, 0);
            tail = new Node(0, 0);
            head.next = tail;
            tail.pre = head;
            size = 0;
        }

        //头部添加节点
        public void addFist(Node node) {
            node.next = head.next;
            node.pre = head;
            head.next.pre = node;
            head.next = node;
            size++;
        }

        //移除节点
        public void remove(Node node) {
            node.pre.next = node.next;
            node.next.pre = node.pre;
            size--;
        }

        //移除末尾节点并返回
        public Node removeLast() {
            if (tail.pre == head) {
                return null;
            }
            Node last = tail.pre;
            remove(last);
            return last;
        }

        //链表长度
        public int size() {
            return size;
        }
    }

    private HashMap<Integer, Node> map;
    private DoubleList list;
    private int cap;

    public LRU(int cap) {
        map = new HashMap<>();
        list = new DoubleList();
        this.cap = cap;
    }

    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        int val = map.get(key).val;
        put(key, val);
        return val;
    }

    public void put(int key, int value) {
        Node node = new Node(key, value);
        if (map.containsKey(key)) {
            list.remove(map.get(key));
            list.addFist(node);
            map.put(key, node);
        } else {
            if (cap == list.size()) {
                Node last = list.removeLast();
                map.remove(last.key);
            }
            list.addFist(node);
            map.put(key, node);
        }
    }
```

## LinkedHashMap
```java
    private LinkedHashMap<Integer, Integer> linkedHashMap;
    private int capacity;

    public LRU_LinkedHashMap(int capacity) {
        this.capacity = capacity;
        linkedHashMap = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return linkedHashMap.size() > capacity;
            }
        };
    }

    public int get(int key) {
        return linkedHashMap.getOrDefault(key, -1);
    }

    public void put(int key, int val) {
        linkedHashMap.put(key, val);
    }
```
