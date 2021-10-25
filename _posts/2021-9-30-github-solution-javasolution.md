---
title: "读取配置文件不覆盖key解决方法"
layout: post
category: note
tags: [solution]
excerpt: "java读取配置文件问题"
---


# 前言
*在公司日常开发过程中遇到了一个问题 需要读取配置文件中重复的key不被覆盖 发现使用Properties无法实现 因为Properties继承hashtable相同的key无法重复 通过面向百度编程找到一个解决方案 故此记录一下*


# 解决方案
## 1.文件读取方式修改
使用Properties已经无法满足我们的需求了 故需要引入apache的工具类 org.apache.commons.configuration2.PropertiesConfiguration

maven引用依赖 选择你喜欢的version 只要你本地或远程仓库有就完事了
```
<dependency>
	  <groupId>org.apache.commons</groupId>
	  <artifactId>commons-configuration2</artifactId>
	  <version>2.1.1</version>
	</dependency>
```
## 2.具体代码实现
我们已经有新的读取配置文件方式了 但是PropertiesConfiguration存储配置文件信息的数据结构是LinkedHashMap还是不能存在重复key值 只是读取时将重复key的配置放到了 object中 这边我们还需要一些处理 并需要用IdentityHashMap接收该结果
```
public class PropertiesUtil { 
    private String filePath;  
    private PropertiesConfiguration propertiesConfiguration;
    
    public IdentityHashMap<String,String>  PropertiesConfigurationLoad(String filePath) throws Exception{
        this.filePath = filePath;  
        filePath = PreventInjectionUtility.pathManipulation(filePath);
        File file = new File(filePath);
        InputStreamReader is = null;
        FileInputStream inStream = new FileInputStream(file);  
        try{
			is = new InputStreamReader(inStream, "UTF-8");
			propertiesConfiguration = new PropertiesConfiguration();  
			propertiesConfiguration.read(is); 
			Iterator<String> keys =  propertiesConfiguration.getKeys();
			IdentityHashMap<String,String> map = new IdentityHashMap<String, String>();
			while(keys.hasNext()) {
				String key = keys.next();
				String[] values = propertiesConfiguration.getStringArray(key); //获取重复key的values
				if(values != null) {
					if(values.length>= 1)
					{
						for(String v:values)
						{
							map.put(new String(key),v); //对值重复key创建新的引用 
						}
					}
				}
			}
			return map;
        } catch(Exception ex){   
        	logger.error("获取文件属性值出错:"+filePath, ex);
        } finally {
    		CloseUtil.close(is);
    		CloseUtil.close(inStream);
        }
        return  null;
    	
    }
    }
```
这边有个重要的知识点 IdentityHashMap对于key是否重复 只比较引用 不比较值是否相同 所以实现了key值可以重复
另外IdentityHashMapJdk源码自带的集合类。无需添加第三方jar包 之类的
## 3.进行你的业务处理
我们的业务要求读取到重复key值然后再放到map<String,List<DataFieldMapping>>中 所以又遍历了一遍IdentityHashMap 对于相同的key值 放到同一个key的对应的list中
```
样例代码待补充 
```
这部分代码大家可以自己发挥 suit yourself.

# 后续

功能暂时是实现了 但是还不确定会不会有其他问题 主要疑虑在IdentityHashMap上 
好像该map不保证顺序 且线程不安全 
而且最好设置一个初始大小
这个类有一个用于调优的参数--最大容量。这个参数用于确定最初包含哈希表的桶数。预期最大尺寸和桶数之间的精确关系未指定。map的大小超过预期值，桶的数量就会增加，这个过程的代价可能十分高，所以最好创建一个合适大小的map。

balabala 就先这样吧