# Chainhash1
チェイン法によるハッシュ1

//相称型（ジェネリックス）を使っている．今回，KはInteger，VはData型である．
//Data型のメンバーは，会員番号を示すIntegerと氏名を示すStringである．

public class ChainHash<K,V> {
	class Node<K,V> {
		private K key; // キー値，今回は会員番号
		private V data; // データ，今回は氏名
		private Node<K,V> next; // 次のノード
		
		Node(K key, V data, Node<K,V> next) {
			this.key = key; // 会員番号
			this.data = data; // 氏名
			this.next = next; // 次のノード
		}
		K getKey() {
			return key;
		}
		
		V getValue() {
			return data;
		}
		
		public int hashCode() {
			return key.hashCode();
		}
	}
	
	private int size; // ハッシュ表のサイズ（今回は13となる）
	private Node<K,V>[] table; // ハッシュ表
	
	public ChainHash(int capacity) {
		// コンストラクタ，「capacity」はハッシュ表のサイズ（この例では13となる）
		try {
			table = new Node[capacity];
			this.size = capacity; // ハッシュ票のサイズ13を格納
		} catch (OutOfMemoryError e) { // 記憶容量が足りなくなった場合
			this.size = 0; // ハッシュ表が作れない場合，零を入れる．
		}
	}
	
	public int hashValue(Object key) { // ハッシュ値の算出
		return key.hashCode() % size;
	}
	
	public V search(K key) {
		int hash = hashValue(key); // ハッシュ値の算出
		Node<K,V> p = table[hash]; // ハッシュ値のノードを持ってくる
		
		while (p != null) {
			if (p.getKey().equals(key))
				return p.getValue(); // 探索成功
			p = p.next; // 次がないノードまで見ていき
		}
		return null; // 探索失敗
	}
	public int add(K key, V data) {
		int hash = hashValue(key); // ハッシュ値の算出
		Node<K,V> p = table[hash]; // ハッシュ値のノードを持ってくる
		
		while (p!=null) {
			if (p.getKey().equals(key)) // このキー値は登録済み
				return 1;
			p = p.next; // 次がないノードまで見ていき
		}
		Node<K,V> temp = new Node<K,V>(key, data, table[hash]);
		table[hash] = temp; // ノードを挟み込む
		return 0;
	}
	
	public int remove(K key) {
		int hash = hashValue(key); // ハッシュ値の算出
			Node<K,V> p = table[hash]; // ハッシュ値のノードを持ってくる
		Node<K,V> pp = null; // 前回の着目ノード
		
		while (p != null) {
			if (p.getKey().equals(key)) { // 見つけたら
				if (pp == null)
					table[hash] = p.next;
				else
					pp.next = p.next;
				return 0;
			}
			pp = p;
			p = p.next; // 後続ノードに着目
		}
		return 1; // そのキー値は存在しない
	}

	public void dump() {
		for (int i = 0; i < size; i++) {
			Node<K,V> p = table[i];
			System.out.printf("%02d ", i);
			while (p != null) {
				System.out.printf("→ %s (%s) ", p.getKey(), p.getValue());
				p = p.next;
			}
			System.out.println();
		}
	}
}
