1.Map集合中允许value为null

2.Map中containsKey()方法用来判断某一个Key是否存在不管映射value是否有值或是null，只要Key存在于Map集合中返回true，否则返回false；


3.Map中get()方法根据Key值获取映射的value，当value为null时返回null，当Key值不存在于Map集合中时也返回null；

测试代码：
    public static void main(String[] args) {
        Map<String,Object> map = new HashMap<String,Object>();
        map.put("111",null);
        map.put("222","222");

        System.out.println("测试get()方法输出");
        System.out.println(map.get("111"));
        System.out.println(map.get("222"));
        System.out.println(map.get("333"));

        System.out.println("测试containsKey()方法输出");
        System.out.println(map.containsKey("111"));
        System.out.println(map.containsKey("222"));
        System.out.println(map.containsKey("333"));
        
    }
    
运行结果：
  测试get()方法输出
  null  222  null
  测试containsKey()方法输出
  true true false
