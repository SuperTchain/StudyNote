##         		Java基础

### 1.重载

方法名称一样参数不同

![](./Img/img1.png)

### 2.数组

![](./Img/img2.png)

### 3.三元运算符，位运算符

![](./Img/img3.png)	

![位运算符](./Img/img48.png)

### 4.API

![](./Img/img4.png)	

### 5.多态

- 多态，简而言之就是==同一个行为具有多个不同表现形式或形态的能力==。
- 多态的分类：运行时多态( 重写式多态 )和编译时多态( 重载式多态 )。
- 运行时多态的前提：继承（实现），重写，向上转型
- 向上转型与向下转型。
- 继承链中对象方法的调用的优先级：this.show(O)、super.show(O)、this.show((super)O)、super.show((super)O)。

![多态的体现](./Img/img6.png)

重载式多态，也叫编译时多态。也就是说这种多态再编译时已经确定好了。重载大家都知道，方法名相同而参数列表不同的一组方法就是重载。在调用这种重载的方法时，通过传入不同的参数最后得到不同的结果。

同一个行为具有多个不同表现形式或形态的能力就是多态，所以我认为重载也是一种多态

重写式多态，也叫运行时多态。这种多态通过动态绑定（dynamic binding）技术来实现，是指在执行期间判断所引用对象的实际类型，根据其实际的类型调用其相应的方法。也就是说，只有程序运行起来，你才知道调用的是哪个子类的方法。
这种多态通过函数的重写以及向上转型来实现，我们上面代码中的例子就是一个完整的重写式多态。我们接下来讲的所有多态都是重写式多态，因为它才是面向对象编程中真正的多态。

#### 5.1向上转型(把子类对象给父类引用)

- 减少重复代码，使代码变得简洁。
- 提高系统扩展性

![多态](./Img/img5.png)

![多态](./Img/img7.png)

![多态的使用](./Img/img8.png)

#### 5.2向下转型(把父类对象转为子类对象)

```java
//还是上面的animal和cat dog
Animal a = new Cat();
Cat c = ((Cat) a);
c.eat();
//输出  我吃鱼
Dog d = ((Dog) a);
d.eat();
// 报错 ： java.lang.ClassCastException：com.chengfan.animal.Cat cannot be cast to com.chengfan.animal.Dog
Animal a1 = new Animal();
Cat c1 = ((Cat) a1);
c1.eat();
// 报错 ： java.lang.ClassCastException：com.chengfan.animal.Animal cannot be cast to com.chengfan.animal.Cat
```

为什么第一段代码不报错呢？相比你也知道了，因为a本身就是Cat对象，所以它理所当然的可以向下转型为Cat，也理所当然的不能转为Dog，你见过一条狗突然就变成一只猫这种操蛋现象？

而a1为Animal对象，它也不能被向下转型为任何子类对象。比如你去考古，发现了一个新生物，知道它是一种动物，但是你不能直接说，啊，它是猫，或者说它是狗。

- 向下转型的前提是父类对象指向的是子类对象（也就是说，在向下转型之前，它得先向上转型）
- 向下转型只能转型为本类对象（猫是不能变成狗的）。

#### 5.3经典案例分析

[多态案例分析]()

注意： 当父类对象引用变量引用子类对象时，被引用对象的类型决定了调用谁的成员方法，引用变量类型决定可调用的方法。如果子类中没有覆盖该方法，那么会去父类中寻找。 (即父类确定引用范围，子类确定引用类型)

```java
class A {
    public String show(D obj) {
        return ("A and D");
    }

    public String show(A obj) {
        return ("A and A");
    }

}

class B extends A{
    public String show(B obj){
        return ("B and B");
    }

    public String show(A obj){
        return ("B and A");
    }
}

class C extends B{

}

class D extends B{

}

public class Demo {
    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new B();
        B b = new B();
        C c = new C();
        D d = new D();

        System.out.println("1--" + a1.show(b));
        System.out.println("2--" + a1.show(c));
        System.out.println("3--" + a1.show(d));
        System.out.println("4--" + a2.show(b));
        System.out.println("5--" + a2.show(c));
        System.out.println("6--" + a2.show(d));
        System.out.println("7--" + b.show(b));
        System.out.println("8--" + b.show(c));
        System.out.println("9--" + b.show(d));
    }
}
//结果：
//1--A and A
//2--A and A
//3--A and D
//4--B and A
//5--B and A
//6--A and D
//7--B and B
//8--B and B
//9--A and D

```

```java
a2.show()分析:
首先，a2是类型为A的引用类型，它指向类型为B的对象。A确定可调用的方法：show(D obj)和show(A obj)。
a2.show(b) ==> this.show(b)，这里this指的是B。
然后.在B类中找show（B obj），找到了，可惜没用，因为show（B obj）方法不在可调用范围内，this.show(O)失败，进入下一级别：super.show(O)，super指的是A。
在A 中寻找show（B obj)，失败，因为没用定义这个方法。进入第三级别：this.show((super)O)，this指的是B。
在B中找show（（A）O）,找到了：show(A obj)，选择调用该方法。
输出：B and A
```

- 先方法，后对象
- this.show(O)、super.show(O)、this.show((super)O)、super.show((super)O)

### 6.集合

![集合类型](./Img/img9.png)

#### 6.1HashSet

![HashSet](./Img/img10.png)

#### 6.2TreeSet

![TreeSet](./Img/img11.png)

![自然排序](./Img/img12.png)

![定值排序](./Img/img13.png)

#### 6.3List

![List集合](./Img/img14.png)

ArrayList线程不安全。

#### 6.4HashMap

![Map](./Img/img15.png)

![HashMap](./Img/img16.png)

Map集合的遍历

![Map的遍历](./Img/img17.png)

第一种遍历效率较低

### ==注意==：==equals hascode区别==

```java
1、“==”
“==”运算符用来比较两个变量的值是否相等。具体情况如下：
（1）如果两个变量是基本数据类型，可以直接使用“==”运算符来比较其对应的值是否相等。
（2）如果一个变量指向的数据是对象（引用类型），此时就会涉及到两块内存，对象本身占用一块内存（堆内存），变量也占用一块内存，变量所对应内存存储的数值就是对象占用的那块内存的首地址。如果要比较两个变量是否指向同一个对象，就可以用“==”来判断。但是如果要比较两个变量所指向对象的内容是否相等，“==”就无能为力了。

2、equals()
equals()是Object类提供的方法之一。每一个Java类都继承Object类，所以每个对象都具有equals()方法。Object类中定义的equals(Object)方法是直接使用“==”运算符比较两个对象，所以在没有覆盖equals(Object)方法的情况下，equals(Object)与“==”运算符一样，比较的是引用。
相比“==”，equals(Object)方法的特殊之处就在于它可以被覆盖，所以可以通过覆盖的方法让它比较数据内容而不是比较引用。

3、hashCode()
hashCode()方法是从Object类中继承过来的，它也用来鉴定两个对象是否相等。Object类中的hashCode()方法返回对象在内存中地址转换成的一个int值，所以如果没有重写hashCode()方法，任何对象的hashCode()方法返回值都是不相等的。
一般在覆盖equals()方法的同时也要覆盖hashCode()方法，否则，就会违反Object.hashCode的通用约定，从而导致该类无法与所有基于散列值（hash）的集合类（HashMap、HashSet和Hashtable）结合在一起正常运行。
hashCode()方法返回值和equals()方法的关系如下：
（1）如果x.equals(y)返回true，即两个对象根据euqals()方法比较是相等的，那么x和y两个对象的hashCode()返回值也是相等的。
（2）x.equals(y)返回false，那么x和y两个对象的hashCode()返回值可能是相等的，也可能不相等。
（3）如果x和y两个对象的hashCode()返回值不相等，一定能推出x.equals(y)返回false。
（4）如果x和y两个对象的hashCode()返回值相等，x.equals(y)可能返回false，也可能返回true。

```



#### 6.5TreeMap

![TreeMap](./Img/img18.png)

### 7.泛型

![泛型](./Img/img19.png)

![泛型通配符](./Img/img20.png)

### 8.Anntation注解

![](./Img/img21.png)

### 8.文件io操作

流的分类

外部输入，内部输出

![流](./Img/img22.png)

#### 8.1==字节流==

![字节流输入](./Img/img23.png)

```java
	//字节流输入
			// 创建文件
		File f = new File("E:/TEST/io.txt");
		if (!f.exists()) {// 当文件不存在的时候进入创建
			// 创建文件
			try {
				f.createNewFile();
				System.out.println("文件创建成功");
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		} else {
			System.out.println("文件已存在");
		}

		// 通过FileInputstream来进行读字节流，读到内存中（磁盘到内存）
		FileInputStream fis = null;
		try {
			fis = new FileInputStream(f);
			int n = 0;// 实际读取到的字节数
			// 定义一个byte数组当做缓冲区
			byte[] bytes = new byte[1024];
			//循环读取
			while ((n = fis.read(bytes)) != -1) {// 没读完
				// 将其转化成字符串
				String s = new String(bytes, 0, n);
				System.out.println(s);
			}

		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				fis.close();
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}

	}
```



![字节流输出](./Img/img24.png)

```java
//字节流输出
public static void main(String[] args) throws IOException {
		// TODO Auto-generated method stub
		File f = new File("E:/TEST/io.txt");
		if (!f.exists()) {
			try {
				f.createNewFile();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		} else {
			System.out.println("文件已存在,写入的内容将覆盖原内容");
		}

		// 通过FileOutputStream输出内容
		FileOutputStream fos = null;
		try {
			fos = new FileOutputStream(f);

			String s1 = "这是iodemo2的输出文件\r\n";// \r\n换行符
			String s2 = "测试换行的文字123qw!";
			// 把String->byte数组
			fos.write(s1.getBytes());
			fos.write(s2.getBytes());
			System.out.println("写入成功");
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				fos.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
```



#### 8.2==字符流==

![字符流输入](./Img/img25.png)

#### 8.3==缓冲流==

![缓冲流](./Img/img26.png)

```java
//字符流缓冲
public static void main(String[] args) {
		// TODO Auto-generated method stub
		// 先定义一个BufferedReader
		BufferedReader br = null;
		// 定义一个BufferedWriter
		BufferedWriter bw = null;
		// 先写入内存，再从内存中输出
		try {
			// 因为BufferedWriter,Reader比FileWriter,Reader高级所以先创建低级对象，再用高级调用
			FileReader fr = new FileReader("E:/TEST/字符流.txt");// 创建输入流文件
			br = new BufferedReader(fr);
			FileWriter fw = new FileWriter("E:/TEST2/输出2.txt");// 创建输出流文件
			bw = new BufferedWriter(fw);
			String s = "";
          // 循环读入
			while ((s = br.readLine()) != null) {
          // 输出内容
				bw.write(s + "\r\n");// 让输出的每一行换行
			}
		} catch (Exception e) {
			// TODO: handle exception
		} finally {
			try {
				br.close();
				bw.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
```

#### 8.4转换流

![转换输入流](./Img/img27.png)

![输出流](./Img/img28.png)

#### 8.5记事本

```java
public class notepad extends JFrame implements ActionListener {
	// 创建文本域
	JTextArea jta = null;
	// 创建菜单条
	JMenuBar jmb = null;
	// 创建菜单
	JMenu jm = null;
	// 创建Item(子菜单)
	JMenuItem jmi1 = null;
	JMenuItem jmi2 = null;

	public static void main(String[] args) {
		notepad nd = new notepad();
	}

	// 初始化
	public notepad() {
		// 设置文本域
		jta = new JTextArea();
		// 创建菜单条,菜单,子菜单
		jmb = new JMenuBar();
		jm = new JMenu("文件");
		jmi1 = new JMenuItem("打开");
		// 注册监听
		jmi1.addActionListener(this);
		jmi1.setActionCommand("open");
		// 设置助记符
		jmi1.setMnemonic('F');// 按alt+f自动展开
		jmi2 = new JMenuItem("保存");
		// 注册监听
		jmi2.addActionListener(this);
		jmi2.setActionCommand("save");

		// 将菜单条加入文本域
		this.setJMenuBar(jmb);
		// 将菜单加入菜单条
		jmb.add(jm);
		// 将子菜单加入菜单
		jm.add(jmi1);
		jm.add(jmi2);

		// 将jta加入JFrame
		this.add(jta);
		// 设置大小
		this.setSize(400, 300);
		this.setTitle("我的记事本");
		this.setVisible(true);
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}

	@Override
	public void actionPerformed(ActionEvent e) {
		// TODO Auto-generated method stub
		// 检测按下按钮是否是open
		if (e.getActionCommand().equals("open")) {

			// JFileChooser文件选择组件
			// 创建组件
			JFileChooser jfs = new JFileChooser();
			// 设置组件名字
			jfs.setDialogTitle("保存为:");
			// 设置默认方式打开
			jfs.showOpenDialog(null);
			// 设置组件可见
			jfs.setVisible(true);
			// 得到用户选择的文件的绝对路径
			String Filename = jfs.getSelectedFile().getAbsolutePath();

			// 读取文件
			FileReader fr = null;
			BufferedReader br = null;
			try {
				fr = new FileReader(Filename);
				br = new BufferedReader(fr);
				// 循环读入
				String s = "";
				String lastCont = "";
				while ((s = br.readLine()) != null) {
					// 换行符
					lastCont += s + "\r\n";
				}
				// 将内容输入到文本域
				jta.setText(lastCont);
			} catch (Exception e2) {
				// TODO: handle exception
				e2.printStackTrace();
			} finally {// 关闭流
				try {
					fr.close();
					br.close();
				} catch (IOException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
			}
		}

		// 检测按下按钮是否是save
		else if (e.getActionCommand().equals("save")) {
			// 创建文件选择组件
			JFileChooser jfs = new JFileChooser();
			// 创建文件名字
			jfs.setDialogTitle("保存在");
			// 设置默认打开
			jfs.showSaveDialog(null);
			// 设置可见
			jfs.setVisible(true);

			// 设置用户选择保存文件的绝对路径
			String file = jfs.getSelectedFile().getAbsolutePath();

			// 输出到指定文件
			FileWriter fw = null;
			BufferedWriter bw = null;
			try {
				fw = new FileWriter(file);
				bw = new BufferedWriter(fw);

				bw.write(this.jta.getText());
			} catch (Exception e2) {
				// TODO: handle exception
				e2.printStackTrace();
			} finally {
				try {// 关闭流
					bw.close();
					fw.close();
				} catch (Exception e3) {
					// TODO: handle exception
					e3.printStackTrace();
				}
			}

		}
	}
}
```

#### 8.6序列化与反序列化

![序列化](./Img/img29.png)

![序列化ID](./Img/img30.png)

### 9.反射

![反射](./Img/img31.png)

![Class类](./Img/img32.png)

![Class类的方法](./Img/img33.png)

![Class类的实例化方法](./Img/img34.png)

![Class类实例化例子](./Img/img35.png)

![通过反射调用方法](./Img/img36.png)

### 10.动态代理

动态代理类实现InvocationHandler

![动态代理](./Img/img37.png)

![动态代理使用](./Img/img38.png)

### 11.线程

![进程的概念](./Img/img39.png)

子进程得到的是除了代码段是与父进程共享以外，其他所有的都是得到父进程的一个副本，子进程的所有资源都继承父进程，得到父进程资源的副本，子进程可获得父进程的所有堆和栈的数据，但二者并不共享地址空间。两个是单独的进程，继承了以后二者就没有什么关联了，子进程单独运行；进程的线程之间共享由进程获得的资源，但线程拥有属于自己的一小部分资源，就是栈空间，保存其运行状态和局部自动变量的。

线程之间共享进程获得的数据资源，所以开销小，但不利于资源的管理和保护；而进程执行开销大，但是能够很好的进行资源管理和保护。

线程的通信速度更快，切换更快，因为他们共享同一进程的地址空间。

一个进程可以有多个线程，线程是进程的一个实体，是CPU调度的基本单位。

![创建线程方法1](./Img/img40.png)

![创建线程的方法二](./Img/img41.png)

![区别](./Img/img42.png)

![Thread线程方法](./Img/img43.png)

![线程的生命周期](./Img/img44.png)

![线程生命周期图](./Img/img45.png)

![同步锁](./Img/img46.png)

普通方法加同步锁，锁的是当前方法对应的对象，当前==对象==的所有加了同步锁的方法共用一个同步锁

静态方法加同步锁，所有对象共用一个同步锁

代码块加同步锁：当前对象的所有synchronized(this)同步的代码都使用同一个锁；synchronized(a)根据传入不同的对象使用不同的锁

![线程的通信](./Img/img47.png)

### 12.==GET请求和POST请求==

1.哪一些情况下，浏览器会发送get请求

- a. 直接在浏览器地址栏输入某个地址

- b. 点击链接

- c. 表单默认的提交方式

2.哪一些情况下，浏览器会发送post请求？

- a. 设置表单method = “post”

3.get请求的特点

- a. 请求参数会添加到请求资源路劲的后面，只能添加少量参数（因为请求行只有一行，大约只能存放2K左右的数据）

- b. 请求参数会显示在浏览器地址栏，路由器会记录请求地址

4.post请求的特点

- a. 请求参数添加到实体内容里面，可以添加大量的参数（也解释了为什么浏览器地址栏不能发送post请求，在地址栏里我们只能填写URL，并不能进入到Http包的实体当中）

- b. 相对安全，但是，post请求不会对请求参数进行加密处理（可以使用https协议来保证数据安全）

[更多详情在这里](https://www.zhihu.com/question/28586791)

### 13.上传下载

#### 1.文件上传

- 要有一个 form 标签，method=post 请求
- form 标签的 encType 属性值必须为==multipart/form-data==值
- 在 form 标签中使用 input type=file 添加上传的文件
- 编写服务器代码（Servlet 程序）接收，处理上传的数据。encType=multipart/form-data 表示提交的数据，以多段（每一个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器。

```jsp
<form action="http://192.168.31.74:8080/09_EL_JSTL/uploadServlet" method="post"
enctype="multipart/form-data">
用户名：<input type="text" name="username" /> <br>
头像：<input type="file" name="photo" > <br>
<input type="submit" value="上传">
</form>
```

1.1导入jar包

commons-fileupload-1.2.1.jar
commons-io-1.4.jar

1.2解析上传信息

```java
package com.atguigu.servlet;


import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.util.List;

public class UploadServlet extends HttpServlet {
    /**
     * 用来处理上传的数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //1 先判断上传的数据是否多段数据（只有是多段的数据，才是文件上传的）
        if (ServletFileUpload.isMultipartContent(req)) {
//           创建FileItemFactory工厂实现类
            FileItemFactory fileItemFactory = new DiskFileItemFactory();
            // 创建用于解析上传数据的工具类ServletFileUpload类
            ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
            try {
                // 解析上传的数据，得到每一个表单项FileItem
                List<FileItem> list = servletFileUpload.parseRequest(req);
                // 循环判断，每一个表单项，是普通类型，还是上传的文件
                for (FileItem fileItem : list) {

                    if (fileItem.isFormField()) {
                        // 普通表单项

                        System.out.println("表单项的name属性值：" + fileItem.getFieldName());
                        // 参数UTF-8.解决乱码问题
                        System.out.println("表单项的value属性值：" + fileItem.getString("UTF-8"));

                    } else {
                        // 上传的文件
                        System.out.println("表单项的name属性值：" + fileItem.getFieldName());
                        System.out.println("上传的文件名：" + fileItem.getName());

                        fileItem.write(new File("e:\\" + fileItem.getName()));
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }

}

```

#### 2.文件下载

response.setHeader("Content-Disposition", "attachment; fileName=1.jpg");这个响应头告诉浏览器。这是需要下载的。而 attachment 表示附件，也就是下载的一个文件。fileName表示下载的文件名

```java
package com.atguigu.servlet;

import org.apache.commons.io.IOUtils;
import sun.misc.BASE64Encoder;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.URLEncoder;

public class Download extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        1、获取要下载的文件名
        String downloadFileName = "2.jpg";
//        2、读取要下载的文件内容 (通过ServletContext对象可以读取)
        ServletContext servletContext = getServletContext();
        // 获取要下载的文件类型/file/文件所在路径
        String mimeType = servletContext.getMimeType("/file/" + downloadFileName);
        System.out.println("下载的文件类型：" + mimeType);
//        4、在回传前，通过响应头告诉客户端返回的数据类型
        resp.setContentType(mimeType);
//        5、还要告诉客户端收到的数据是用于下载使用（还是使用响应头）
        // Content-Disposition响应头，表示收到的数据怎么处理
        // attachment表示附件，表示下载使用
        // filename= 表示指定下载的文件名
        // url编码是把汉字转换成为%xx%xx的格式
        if (req.getHeader("User-Agent").contains("Firefox")) {
            // 如果是火狐浏览器使用Base64编码
            resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode("中国.jpg".getBytes("UTF-8")) + "?=");
        } else {
0            // 如果不是火狐，是IE或谷歌，使用URL编码操作
            resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode("中国.jpg", "UTF-8"));
        }
        /**
         * /斜杠被服务器解析表示地址为http://ip:prot/工程名/  映射 到代码的Web目录
         */
        InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + downloadFileName);
        // 获取响应的输出流
        OutputStream outputStream = resp.getOutputStream();
        //        3、把下载的文件内容回传给客户端
        // 读取输入流中全部的数据，复制给输出流，输出给客户端
        IOUtils.copy(resourceAsStream, outputStream);
    }
}

```



### 14.接口和抽象类

![接口和抽象类的区别](./Img/img49.png)	