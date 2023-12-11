可能是Java最快的字典树 简单 内存占用低
利用哈希表做存储结构 构造完成字典后会自动重构哈希表 优化到可接受的链表深度 Trie每次查找时直接用哈希表的数据直接查速度非常快
```
    public void parseText(String word, int i, int limit, FilterIndex filter) {
		for (; i < limit;) {
			Trie trie = this;
			int j = i;
			for (; j < limit; j++) {
				char key = word.charAt(j);
				Trie next = null;
				UNSAFE_GET: {
					int hash = (int) key;
					for (TrieCharMap.Entry<Trie> m = trie.children.elementData[(hash & (trie.children.elementData.length - 1))]; m != null; m = m.next) {
						if (key == m.key) {
							next = m.value;
							break;
						}
					}
				}
				if ((next == null))
					break;
				trie = next;

				if (trie.pos != NOT_FOUND) {
					int len = j - i;
					if (!filter.accept(trie.pos, word, i, len)) {
						return;
					}
				}
			}
			i++;
		}
	}
```
对比对象 hankcs/AhoCorasickDoubleArrayTrie

内存低了50%（这个不算因为他的结构比较大）
字典加载速度快30倍 
同个大文件检索速度快48%

Fols-Trie
![image](https://github.com/sininit/Trie/blob/main/1.png)

hankcs/AhoCorasickDoubleArrayTrie
![image](https://github.com/sininit/Trie/blob/main/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20231211195601.png)


```java
Fols-Trie
    [2, 0, 1]
    33.02459716796875MB
    加载时间：71毫秒, 字典数量：153151，字典CHAR长度：(401553)
    {$$bufferCountSum=203279, $$bufferCapacitySum=1209074, $$buffMaxLinkedDeep=2, $$bufferCapacityTop=32768}
    加载文章
    加载文章耗时：31744毫秒, 文章大小：852296704 加载速度：(26849064.51612903177738189697265625) char/s
```

```java
hankcs/AhoCorasickDoubleArrayTrie
  [[0:1]=你, [0:2]=你好, [0:3]=你好啊]
  74.07536315917969MB
  加载字典时间：2462毫秒, 字典数量：153151 
  加载文章耗时：50803毫秒, 文章大小：852296704 加载速度：(16776503.4348365254700183868408203125) char/s  
```
