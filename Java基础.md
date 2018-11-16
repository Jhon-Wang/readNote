#
## 集合类
### Collection接口

#### HashMap
##### 基础数据结构
###### 节点
```Java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
```
- 基础结构
    - hash; key hash值
    - key; 键
    - value; 值
    - next; 下一个节点
###### set
###### size
###### modCount
###### threshold 桶数量默认为16
###### loadFactor 加载因子 默认为0.75F 
如果占用了12个桶之后在占用一个桶就会触发rehash 操作
##### 特性集
###### 插入
- 流程：插入列表检查列表是否为空，如果为空触发resize 操作，resize过程 如果原来的列表为空那么会创建一个默认大小为16 加载因子为0.75 的列表，进行重新hash操做复杂原有的元素；检查n-1 & hash 为下标的位置是不是为空如果为空那么直接新建一个Node 插入到此位置，如果此位置已经有值那么，如果第一个键正好是要插入的键那么替换旧值，如果要插入的值是一个树节点那么使用树节点，如果第一个节点不是那么就在桶内依次对比，如果找到同样的key值那么就替换值，如果找到最后还是没找到相同的值那么就在在最后添加新节点
###### 查找
通过hash 值计算桶位置 查找第一个在依次查找桶中元素，如果桶的key跟输入的参数相同返回节点在节点中返回value
###### 重新设置值
###### 查找是否有某个key


## 并发包
## IO/NIO
## JVM
## 内存模型
## 泛型
## 异常
## 反射
