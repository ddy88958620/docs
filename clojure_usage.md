#### ������ݹ�
+ doseqִ�в����󷵻�nil

	;;��ӡ0-4
	(doseq [x (range 5)]
	  (println x))
	>0
	>1
	>2
	>3
	>4
	>nil
	
+ for����vector

	(for [x (range 5)]
	  (println x))
	>0
	>1
	>2
	>3
	>4
	>(0 1 2 3 4)

+ loop�ݹ鴦��

	(defn fac [n]
	  (loop [cnt n res 1]
	    (if (<= cnt 0) res
		  (recur (dec cnt) (* cnt res)))))	
	(fac 10)

#### ���������
+ ��������:`(fn [n] (println n))`
+ �󶨱���:`(def b-n "bind value.")`
+ �󶨺���:`(def b-n-len (fn [] (count b-n)))`��`(defn b-n-len [] (count b-n))`��ͬ
+ �󶨺���Ҳ����ʹ��`#(...)`�ķ�ʽ:`(def b-n-len #(count b-n))`
+ �����п���ʹ��`%`��ʾһ��������Ҳ������`%[���к�]`��ʾ�ڼ�������:`(def ex-len #(+ (count %1) (count %2))`
+ �̶�����:

	(defn add [v1 v2 v3 v4]
	  (+ v1 v2 (if v3 v3 0) (if v4 v4 0)))
	;;����ʹ�������ģʽƥ�䷽ʽ����
	(defn add
	  [v1 v2] (+ v1 v2)
	  [v1 v2 v3] (+ v1 v2 v3)
	  [v1 v2 v3 v4] (+ v1 v2 v3 v4))

+ �ɱ����:

	(defn add [v1 v2 & others]
	  (+ v1 v2 (if others (reduce + 0 others) 0)))
