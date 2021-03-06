﻿## Groovy

#### 在Linux脚本中运行
+ 编辑hello文件，加入以下内容：

	#!/usr/bin/env groovy
	println("Hello World!")

Closure闭包
map = ['a':1,'b':2]
//1.直接使用闭包
map.each {key,value -> println "$key = $value" }
//2.定义闭包后使用
def pt = { key,value -> println "$key : $value " }
map.each pt
map.each(pt)
//2.1也可以使用闭包自身调用方法
pt.call(key,value)
pt(key,value)
//3.使用函数闭包
def pt2(entry){
	def key=entry.key
	def value=entry.value
	println "$key @ $value"
}
def pt2c = this.&pt2
map.each pt2c 
map.each(pt2c)

闭包的接续
def min = { x,y -> [x,y].min() }
//1
min(1,3)
//2
def triple=min.curry(1)
triple(3)
//3
min.curry(1)(3)

元编程ExpandoMetaClass(EMC)
扩展String类
String.metaClass.addSign = { name ->
	println "$name"
	delegate.toUpperCase()
}
引入一个概念：委托（delegate）。delegate 是 EMC 所围绕的类。
扩展Math类静态方法
Math.metaClass.static.random= {
	0.5
}
[Class].metaClass 设置为 null，就可以取消元编程。 

如果您不希望扩展方法出现在所有 String中，可以仅调整单一实例的 EMC（而不是类）
String single='Hello World'
single.metaClass.exSign = { name ->
	println "$name"	
}

正则表达式
Groovy中使用//表示正则表达式
如/[a-z]+/就是一个正则表达式
~ 用在字符串之前，则将字符串编译成Pattern
def pattern = ~/[a-zA-Z]+/

=~ 将操作符左边的字符串跟右边的Pattern进行局部匹配，返回值为Matcher
def matcher = ("find"=~/[a-zA-Z]+/)
println matcher.find()		
println matcher[0]

其中Matcher的find方法为递归方法，可以用来分组
def matcher= ("find the name, Mr. Json." =~ /\w+/)
while(matcher.find()){
	println "${matcher.group()}"
}

使用matcher[0]之前，调用find()做一下判断.
def matcher = "foobFFazaarquux" =~ "o(b.*za)(.*)q"
def lst=[]
if(matcher.find()){
   list=matcher[0]
   println "${list},${list.size()},${list[0]},${list[1]},${list[2]}"
}


==~ 用法跟=~类似，只是这里进行的精确匹配，即左边的整个字符串跟左边的模式进行匹配，==~的结果跟Matcher.matches()的结果是一样的。返回值为Boolean。
def mtb = ("find"==~/[a-zA-Z]+/)
assert mtb.getClass()==java.lang.Boolean

注意：
正则表达式//中可以使用GString
def pstr='[a-zA-Z]+'
def pattern = ~/$pstr/


对字符串的改进
replaceFirst 将满足正则表达式的第一个字符串进行替换，比如：
assert "Green Eggs and Spam" == "Spam Spam".replaceFirst(/Spam/, "Green Eggs and") 

replaceAll 将所有满足正则表达式的字符串进行替换，比如：
assert "The armor was colored silver" == "The armour was coloured silver".replaceAll(/ou/, "o")  

replaceAll的第二个参数可以使用闭包，但需要注意，闭包中的第一个参数是匹配的字符串。
def dashedToCamelCase(og) {
    og.replaceAll(/-(\w)/){fullMatch,firstCharacter->firstCharacter.toUpperCase()}
}
assert "firstName" == dashedToCamelCase("first-name")
tokenize  分割字符串返回List，接收分隔符默认使用空格分词。分隔符为字符列表[',','!']也可以使用",!"
def strList="""Hello World!".tokenize();
result:["Hello","World!"]


循环的使用
3.times { print "${it} "}
0 1 2
0.upto(2) { print "${it} "}
0 1 2
0.step(3,1) { print "${it} "}
0 1 2

进程
def proc="echo 'Hello World!'".execute()
println "${proc.in.text},${proc.err.text}" //or println "${proc.text}"
需要注意的是${proc.in.text}或者${proc.text}调用后自动关闭流，如果再次调用${proc.in.text}或者${proc.text}会报错。


XML处理
使用MarkupBuilder同步构建简单的XML文档。
import groovy.xml.MarkupBuilder

def writer=new StringWriter()
//写XML内容
def xml=new MarkupBuilder(writer)
xml.persons() {
	4.times {		
		person(id:it,name:"My name is [${it}]"){
			child("This is child ${it}.")
		}
	}
}
//对于更加高级的 XML 创建，Groovy 提供了一个 StreamingMarkupBuilder。通过它，您可以添加各种各样的 XML 内容，比如说处理指令、名称空间和使用 mkp 帮助对象的未转义文本（非常适合 CDATA 块）。
import groovy.xml.StreamingMarkupBuilder
def writer=new StringWriter()
def comment = "<![CDATA[<!-- address is new to this release -->]]>"
def persons={
	mkp.xmlDeclaration()
	mkp.pi("xml-stylesheet": "type='text/xsl' href='myfile.xslt'" )
	mkp.declareNamespace('':'http://myDefaultNamespace')
	mkp.declareNamespace('location':'http://someOtherNamespace')
	persons{
		4.times {		
			person(id:it,name:"My name is [${it}]"){
				child("This is child ${it}.")
			}
		}
	}
}
def sxml = new groovy.xml.StreamingMarkupBuilder()
sxml.encoding = "UTF-8"
writer<<sxml.bind(persons)
println writer.toString()

//读XML内容 XmlParser
def pvalues=new XmlParser().parseText(writer.toString())
pvalues.person.each {
	println "id:${it.attribute('id')},name:${it.attribute('name')},child:${it.child.text()}"
}
println writer.toString()
//读XML内容 XmlSlurper
def values=new XmlSlurper().parseText(writer.toString())
values.person.each {
	println "id:${it.@id},name:${it.@name},child:${it.child}"
}
println writer.toString()

Groovy与Maven的集成
使用GMaven插件，目前SVN地址:https://svn.codehaus.org/gmaven/。
缺省的源码目录为src/main/groovy，src/test/groovy。
在Maven配置文件pom.xml中添加以下内容：
<dependencies>
	<dependency>
    	<groupId>org.codehaus.gmaven</groupId>
      	<artifactId>gmaven-mojo</artifactId>
        <version>${gmaven.ver}</version>
    </dependency>
    <dependency>
        <groupId>org.codehaus.gmaven.runtime</groupId>
        <artifactId>gmaven-runtime-1.7</artifactId>
        <version>${gmaven.ver}</version>
    </dependency>
</dependencies>
<build>
	<plugins>
		<plugin>
    		<groupId>org.codehaus.gmaven</groupId>
	        <artifactId>gmaven-plugin</artifactId>
    	    <version>${gmaven.ver}</version>
        	<executions>
	        	<execution>
    	        	<goals>
        	        	<goal>compile</goal>
                	    <goal>testCompile</goal>
                    	<goal>generateStubs</goal>
	                    <goal>generateTestStubs</goal>
        	        </goals>
            	</execution>
	        </executions>
    	</plugin>		
	</plugins>
</build>
使用命令mvn clean groovy:compile compile jar:jar 自动编译groovy源文件为class文件并打包。

Groovy与Ant的集成
由于gmaven插件的开发目前处于停滞状态，所以推荐使用maven-antrun-plugin插件。
<build>
	<plugins>
		<plugin>
			<artifactId>maven-antrun-plugin</artifactId>
			<executions>
				<execution>
					<id>compile</id>
					<phase>compile</phase>
					<configuration>
						<tasks>
							<mkdir dir="${basedir}/src/main/groovy"/>
							<taskdef name="groovyc"
							         classname="org.codehaus.groovy.ant.Groovyc">
								<classpath refid="maven.compile.classpath"/>
							</taskdef>
							<mkdir dir="${project.build.outputDirectory}"/>
							<groovyc destdir="${project.build.outputDirectory}"
							         srcdir="${basedir}/src/main/groovy/" listfiles="true">
								<classpath refid="maven.compile.classpath"/>
							</groovyc>
						</tasks>
					</configuration>
					<goals>
						<goal>run</goal>
					</goals>
				</execution>
				<execution>
					<id>test-compile</id>
					<phase>test-compile</phase>
					<configuration>
						<tasks>
							<mkdir dir="${basedir}/src/test/groovy"/>
							<taskdef name="groovyc"
							         classname="org.codehaus.groovy.ant.Groovyc">
								<classpath refid="maven.compile.classpath"/>
							</taskdef>
							<mkdir dir="${project.build.testOutputDirectory}"/>
							<groovyc destdir="${project.build.testOutputDirectory}"
							         srcdir="${basedir}/src/test/groovy/" listfiles="true">
								<classpath refid="maven.test.classpath"/>
							</groovyc>
						</tasks>
					</configuration>
					<goals>
						<goal>run</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
	</plugins>
</build>	