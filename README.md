# the-management-of-course
manage student's course at school 
from tkinter import *                                                               #导入tkinter
from tkinter import messagebox                                                      #引入弹出框
#from typing import Any, Union
import pymysql                                                                       #导入pymysql
from pymysql.cursors import Cursor                                                  #导入游标
from hashlib import sha1                                                            #导入hashlib,引用函数
import tkinter as tk

#开始类的定义


class HomePage:                                                                       #窗口1，我的首页
    def __init__(self):
        self.conn =  pymysql.Connect(
                            host='localhost',
                            port=3306,
                            user='root',
                            passwd='12345',
                            db='天朝理工学院',
                            charset="utf8"
                    )                                                                  #连接数据库

        self.window=Tk()                                                               #窗体设计
        self.window.title("课程管理系统")
        self.window['width']=300
        self.window['height']=400
        self.window['bg']='pink'
        self.window.geometry('400x600-100-100')

        self.f1=Frame(self.window);self.f1.pack()
        self.f2=Frame(self.window);self.f2.pack()
        self.f3=Frame(self.window);self.f3.pack()
        self.f4=Frame(self.window);self.f4.pack()
        self.f5=Frame(self.window);self.f5.pack()
        self.f6=Frame(self.window);self.f6.pack()
        self.f7=Frame(self.window);self.f7.pack()

        #f3是账号的框架
        #f4是密码的框架

        Label(self.f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='pink').pack()
        Label(self.f2,text="天朝皇家理工学院\n 学生课程内容管理中心\n     \n    \n    \n",
              font=('Helvetica',14,'bold'),bg='pink').pack()

        Label(self.f3,text="账号").pack(side=LEFT)

        self.account=StringVar()
        self.password=StringVar()
        #get()用于返回StringVar变量的值,相当于一个函数

        Entry(self.f3,validate='key',textvariable=self.account).pack(side=LEFT)
        Label(self.f4,text="密码").pack(side=LEFT)
        Entry(self.f4,textvariable=self.password).pack(side=LEFT)
        Label(self.f5,text="",bg='pink').pack()

        Button(self.f6,text="登录",command=self.login).pack(side=RIGHT)
        Button(self.f6,text="注册",command=self.register).pack(side=RIGHT)
        Button(self.f6,text="帮助",command=self.help).pack(side=RIGHT)

        self.mutex=0                                                            #mutex的值表示当前是哪个窗体

        self.window.mainloop()
        
    def help(self):                                                             #帮助按钮事件，弹出信息
        str1 = '欢迎进入天朝理工学院课程管理系统！\n 你可以直接使用用户名和账号直接登陆。\n 新用户请先注册！'
        messagebox.showinfo('help',str1)

    def register(self):                                                        #注册按钮事件
        v3= str(self.account.get())                                             #获取文本框输入的内容
        v4= str(self.password.get())

        if(v3):                                                                #首页填了账号的话，需要把输入框账号清空才可注册
            messagebox.showinfo('error', "请先点击注册进入注册页面\n 再开始注册！")
        else:
            self.mutex=2                                                        #值设置为2，进入第二个窗口注册页面
            self.window.destroy()                                               #销毁当前窗口
            
    def login(self):                                                            #登录按钮事件
        v3= str(self.account.get())
        v4= str(self.password.get())
        cursor = self.conn.cursor()                                              #启动游标
        cursor.execute( "select * from login where `account`='%s'"% v3)       #注意引号使用
        data = cursor.fetchone()                                                  #返回单个元组
        self.conn.commit()
        
        if data:
            if data[1]==v4:                                                       #对应密码
                self.mutex=3                                                      #指示值设置为3，指示进入operation界面
                self.account=v3
                self.password=v4
                cursor.close()                                                    #
                self.conn.close()
                self.window.destroy()
            else:
                messagebox.showinfo('error', "密码错误！")
        else:
            messagebox.showinfo('error', "此用户不存在！\n请先注册！")


class RegisterPage:                                                              #窗口2注册页面
    def __init__(self):
        self.window=Tk()
        self.window.title("课程管理系统-注册")
        self.window['width']=300
        self.window['height']=400
        self.window['bg']='pink'
        self.window.geometry('400x600-100-100')

        self.f1=Frame(self.window);self.f1.pack()
        self.f2=Frame(self.window);self.f2.pack()
        self.f3=Frame(self.window);self.f3.pack()
        self.f4=Frame(self.window);self.f4.pack()
        self.f5=Frame(self.window);self.f5.pack()
        self.f6=Frame(self.window);self.f6.pack()
        self.f7=Frame(self.window);self.f7.pack()

        self.account=StringVar()
        self.password=StringVar()
        self.again_password=StringVar()

        Label(self.f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='pink').pack()
        Label(self.f2,text="天朝皇家理工学院\n 学生课程内容注册中心\n     \n    \n    \n",font=('Helvetica',14,'bold'),bg='pink').pack()
        Label(self.f3,text="账号").pack(side=LEFT)
        Entry(self.f3,textvariable=self.account).pack(side=LEFT)
        Label(self.f4,text="密码").pack(side=LEFT)
        Entry(self.f4,textvariable=self.password).pack(side=LEFT)
        Label(self.f5,text="确定").pack(side=LEFT)
        Entry(self.f5,textvariable=self.again_password).pack(side=LEFT)
        Button(self.f6,text="注册",command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def c1(self):                                                                   #注册按钮事件
        v3 = str(self.account.get())
        v4 = str(self.password.get())
        v5 = str(self.again_password.get())

        if(v5!=v4):
            messagebox.showinfo(title='error', message='密码不一致！')
        elif not v3:
            messagebox.showinfo(title='error', message='账号不能为空！')
        elif (not v4) or (not v5):
            messagebox.showinfo(title='error', message='密码不能为空！')
        else:
            conn =  pymysql.Connect(
                host='localhost',
                port=3306,
                user='root',
                passwd='12345',
                db='天朝理工学院',
                charset="utf8"
             )                                                                         # 连接数据库

            cursor = conn.cursor()
            read_sql = "SELECT * FROM login "                                         #查询已有的数据
            print(read_sql)
            cursor.execute(read_sql)
            conn.commit()
            results=cursor.fetchall()                                                   #获取所有数据列表

            for row in results:
                print(row)                                                              #测试
                if row[0] == v3:
                    messagebox.showwarning(title='警告！', message='账户已存在，请重新输入')
                    return 0
            #sql = "insert into login(account,password) values(%s,%s)on duplicate"（测试）
            sql="INSERT INTO login(account,password) VALUES('%s','%s');" %(v3,v4)  #插入数据
            #value = (( v3, v4 ))
            cursor.execute(sql)
            conn.commit()
            cursor.close()
            conn.close()

            messagebox.showinfo(title='connected', message='注册成功！')

            self.mutex=1                                                                 #值设置为1，进入第1个窗口登陆页面

            self.window.destroy()


class OperationPage:                                                                     #窗口3operation界面
    def f_print(self):
        tk.messagebox.showinfo('天朝理工', '课程信息中心')

    def set_mutex(self,mutex):
        self.mutex=mutex
        self.window.destroy()

    def __init__(self):
        self.window=tk.Tk()
        self.window.title("课程管理系统")
        self.window['width']=300
        self.window['height']=400
        self.window.geometry('400x600-100-100')
        self.window['bg']='gray'

        f1=Frame(self.window);f1.pack()
        f2=Frame(self.window);f2.pack()

        Label(f1,text="\n \n \n\n\n\n同学你好\n\n欢迎进入\n \n",font=('Helvetica',12,'bold'),bg='gray').pack()
        Label(f2,text="天朝皇家理工学院\n学生课程内容管理中心\n     \n!!!!!!\n    \n",
              font=('Helvetica',14,'bold'),bg='gray').pack()

        menubar=tk.Menu(self.window)
        menuFile=tk.Menu(menubar)
        menuOSM=tk.Menu(menubar,tearoff=0)
        menuCourse=tk.Menu(menubar,tearoff=0)
        menuMessage=tk.Menu(menubar,tearoff=0)
        menuSellect=tk.Menu(menubar,tearoff=0)
        menuExit=tk.Menu(menubar)

        menubar.add_cascade(label='File',menu=menuFile)
        menubar.add_cascade(label='系统管理',menu=menuOSM)
        menubar.add_cascade(label='课程信息',menu=menuCourse)
        menubar.add_cascade(label='公告',menu=menuMessage)
        menubar.add_cascade(label='选课',menu=menuSellect)
        menubar.add_cascade(label='退出',menu=menuExit)

        menuFile.add_command(label='Open')
        menuFile.add_command(label='Save')
        menuFile.add_command(label='Print',accelerator='^P',command=self.f_print)
        menuFile.add_separator()

        menuOSM.add_command(label='个人信息查看',command=lambda : self.set_mutex(mutex=4.1))
        menuOSM.add_command(label='修改信息',command=lambda : self.set_mutex(mutex=4.2))
        menuOSM.add_separator()

        menuCourse.add_cascade(label='所选课程',command=lambda : self.set_mutex(mutex=6.1))
        #(label='所选课程',menu=menusub)
        menuCourse.add_cascade(label='教师信息介绍',command=lambda : self.set_mutex(mutex=6.2))
        menuCourse.add_cascade(label='课程介绍',command=lambda : self.set_mutex(mutex=6.3))
        menuCourse.add_separator()

        menuMessage.add_command(label='通告',command=lambda : self.set_mutex(mutex=6))
        menuMessage.add_separator()

        menuprogram=tk.Menu(menuSellect)
        menuclass=tk.Menu(menuSellect)

        menuSellect.add_cascade(label='按年级',menu=menuprogram)
        menuSellect.add_cascade(label='按类别',menu=menuclass)

        menuprogram.add_command(label='大一上',command=lambda : self.set_mutex(mutex=7.11))
        menuprogram.add_command(label='大一下',command=lambda : self.set_mutex(mutex=7.12))
        menuprogram.add_command(label='大二上',command=lambda : self.set_mutex(mutex=7.21))
        menuprogram.add_command(label='大二下',command=lambda : self.set_mutex(mutex=7.22))
        menuprogram.add_command(label='大三上',command=lambda : self.set_mutex(mutex=7.31))
        menuprogram.add_command(label='大三下',command=lambda : self.set_mutex(mutex=7.32))
        menuprogram.add_command(label='大四上',command=lambda : self.set_mutex(mutex=7.41))
        menuprogram.add_command(label='大四下',command=lambda : self.set_mutex(mutex=7.42))

        menuclass.add_command(label='计算机类',command=lambda : self.set_mutex(mutex=8.1))
        menuclass.add_command(label='外语类',command=lambda : self.set_mutex(mutex=8.2))
        menuclass.add_command(label='管理类',command=lambda : self.set_mutex(mutex=8.3))
        menuclass.add_command(label='经济类',command=lambda : self.set_mutex(mutex=8.4))
        menuclass.add_command(label='建筑',command=lambda : self.set_mutex(mutex=8.5))
        menuclass.add_command(label='纺织类',command=lambda : self.set_mutex(mutex=8.6))
        menuclass.add_command(label='通识类',command=lambda : self.set_mutex(mutex=8.7))
        menuclass.add_command(label='体育类',command=lambda : self.set_mutex(mutex=8.8))
        menuclass.add_command(label='政治类',command=lambda : self.set_mutex(mutex=8.9))
        menuclass.add_command(label='军事类',command=lambda : self.set_mutex(mutex=8.10))
        menuExit.add_command(label='退出系统',command=lambda : self.set_mutex(mutex=8.11))

        self.window['menu']=menubar
        self.mutex=0
        self.window.mainloop()


class kechengjieshao():                                                                          #窗口通告窗口-课程介绍界面
    def __init__(self,account):
        self.account=account
        self.conn =  pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
            )                                                                                     #连接数据库

        root=Tk()
        root.title("课程管理系统")
        root['width']=300
        root['height']=400
        root['bg']='cyan'
        root.geometry('400x600-100-100')

        f1=Frame(root);f1.pack()
        f2=Frame(root);f2.pack()
        f3=Frame(root);f3.pack()
        f4=Frame(root);f4.pack()

        self.val=StringVar()

        Label(f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='cyan').pack()
        Label(f2,text="天朝皇家理工学院\n 学生课程内容管理中心\n通告（课程介绍）\n",font=('Helvetica',14,'bold'),
              bg='cyan').pack()

        Label(f3,text="#内容",bg='cyan',textvariable=self.val).pack(side=LEFT)

        Button(f4,text="确定",command=self.c1).pack(side=LEFT)
        Button(f4,text="退出",command=root.destroy).pack(side=RIGHT)

        self.mutex=3
        root.mainloop()

    def c1(self):                                                                               # 显示所选课程
        cursor = self.conn.cursor()
        read_sql = "SELECT course,'c-summary' FROM teacher"                                  #注意把关键字包装
        cursor.execute(read_sql)
        self.conn.commit()
        results = cursor.fetchall()
        self.val.set(results)
        cursor.close()
        self.conn.close()


class jiaoshixinxijieshao():                                                                    #窗口通告窗口-所选课程界面
    def __init__(self,account):
        self.account=account
        self.conn =  pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
            )                                                                                     #连接数据库

        root=Tk()
        root.title("课程管理系统")
        root['width']=300
        root['height']=400
        root['bg']='cyan'
        root.geometry('400x600-100-100')

        f1=Frame(root);f1.pack()
        f2=Frame(root);f2.pack()
        f3=Frame(root);f3.pack()
        f4=Frame(root);f4.pack()

        self.val=StringVar()

        Label(f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='cyan').pack()
        Label(f2,text="天朝皇家理工学院\n 学生课程内容管理中心\n通告（课程介绍）\n",font=('Helvetica',14,'bold'),
              bg='cyan').pack()

        Label(f3,text="#内容",bg='cyan',textvariable=self.val).pack(side=LEFT)

        Button(f4,text="确定",command=self.c1).pack(side=LEFT)
        Button(f4,text="退出",command=root.destroy).pack(side=RIGHT)

        self.mutex=3
        root.mainloop()

    def c1(self):                                                                               # 显示所选课程
        cursor = self.conn.cursor()
        read_sql = "SELECT `T-name`,`t-summary` FROM teacher  "
        cursor.execute(read_sql)
        self.conn.commit()
        results = cursor.fetchall()
        self.val.set(results)
        cursor.close()
        self.conn.close()


class suoxuankecheng():                                                                        #通告-所选课程界面
    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                                       # 连接数据库

        root = Tk()
        root.title("课程管理系统")
        root['width'] = 300
        root['height'] = 400
        root['bg'] = 'cyan'
        root.geometry('400x600-100-100')

        f1 = Frame(root);
        f1.pack()
        f2 = Frame(root);
        f2.pack()
        f3 = Frame(root);
        f3.pack()
        f4 = Frame(root);
        f4.pack()

        self.val = StringVar()

        Label(f1, text="欢迎进入！\n  \n", font=('Helvetica', 12, 'bold'), bg='cyan').pack()
        Label(f2, text="天朝皇家理工学院\n 学生课程内容管理中心\n通告（所选课程）\n", font=('Helvetica', 14, 'bold'),
              bg='cyan').pack()

        Label(f3, text="#内容", bg='cyan', textvariable=self.val).pack(side=LEFT)

        Button(f4, text="确定", command=self.c1).pack(side=LEFT)
        Button(f4, text="退出", command=root.destroy).pack(side=RIGHT)

        self.mutex = 3
        root.mainloop()

    def c1(self):                                                                                # 显示所选课程
        cursor = self.conn.cursor()
        read_sql = "SELECT course FROM selected where `account`='%s'" % self.account
        cursor.execute(read_sql)
        self.conn.commit()
        results = cursor.fetchall()
        self.val.set(results)
        cursor.close()
        self.conn.close()


class Person_Information_Review_Page():                                                         #个人信息查看窗口
    def __init__(self,account,password):
        self.account=account
        self.password=password

        root=Tk()
        root.title("课程管理系统")
        root['width']=300
        root['height']=400
        root['bg']='cyan'
        root.geometry('400x600-100-100')
        
        f1=Frame(root);f1.pack()
        f2=Frame(root);f2.pack()
        f3=Frame(root);f3.pack()
        f4=Frame(root);f4.pack()
        #f5=Frame(root);f5.pack()                                                                 #放弃连接表啊哈哈
        #f6=Frame(root);f6.pack()
        f7=Frame(root);f7.pack()
        f8=Frame(root);f8.pack()
        f9=Frame(root);f9.pack()
        f10=Frame(root);f10.pack()
        f11=Frame(root);f11.pack()
        f12=Frame(root);f12.pack()
        f13=Frame(root);f13.pack()
        f14=Frame(root);f14.pack()
        f15=Frame(root);f15.pack()
        f16=Frame(root);f16.pack()
        f17=Frame(root);f17.pack()
        f18=Frame(root);f18.pack()
        f19=Frame(root);f19.pack()

        Label(f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='cyan').pack()
        Label(f2,text="天朝皇家理工学院\n 学生课程内容管理中心\n个人信息查看\n",
              font=('Helvetica',14,'bold'),bg='cyan').pack()

        Label(f3,text="用户名",bg='cyan').pack(side=LEFT)
        self.label_account=Label(f4,text="",bg='cyan');self.label_account.pack(side=LEFT)
        #Label(f4,text="").pack(side=LEFT)                                              #从数据库选数据显示在指定位置

        #Label(f5,text="密码",bg='cyan').pack(side=LEFT)
        #self.label_password=Label(f6,text="",bg='cyan');self.label_password.pack(side=LEFT)
        #Label(f6,text="").pack()

        Label(f7,text="班级",bg='cyan').pack(side=LEFT)
        self.label_class_v=Label(f8,text="",bg='cyan');self.label_class_v.pack(side=LEFT)
        #Label(f8,text="").pack()

        Label(f9,text="系别",bg='cyan').pack(side=LEFT)
        self.label_xibie=Label(f10,text="",bg='cyan');self.label_xibie.pack(side=LEFT)
        #Label(f10,text="",bg='pink').pack()

        Label(f11,text="辅导员",bg='cyan').pack(side=LEFT)
        self.label_f_teacher=Label(f12,text="",bg='cyan');self.label_f_teacher.pack(side=LEFT)
        #Label(f12,text="").pack()

        Label(f13,text="calls",bg='cyan').pack(side=LEFT)
        self.label_call=Label(f14,text="",bg='cyan');self.label_call.pack(side=LEFT)
        #Label(f14,text="").pack()

        #Label(f15,text="年级",bg='cyan').pack(side=LEFT)
        #self.label_grade=Label(f16,text="",bg='cyan');self.label_grade.pack(side=LEFT)
        #Label(f16,text="").pack()

        Label(f17,text="姓名",bg='cyan').pack(side=LEFT)
        self.label_s_name=Label(f18,text="",bg='cyan');self.label_s_name.pack(side=LEFT)
        #Label(f18,text="").pack()

        Button(f19,text="确定",command=self.c1).pack(side=LEFT)
        Button(f19,text="退出",command=root.destroy).pack(side=RIGHT)

        self.conn = pymysql.Connect(
                host='localhost',
                port=3306,
                user='root',
                passwd='12345',
                db='天朝理工学院',
                charset="utf8"
            )                                                                          #连接数据库
        root.mainloop()
        self.mutex=3

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute( "select * from student where `account`='%s'"% self.account)
        self.conn.commit()
        data = cursor.fetchone()

        s_name,account,class_v,xibie,f_teacher,call,grade=data
        val={}

        val['account']=StringVar()                                                  #账号
        self.label_account["textvariable"] =val['account']
        val['account'].set(self.account)

        val['f_teacher']=StringVar()                                               #辅导员
        self.label_f_teacher["textvariable"] =val['f_teacher']
        val['f_teacher'].set(f_teacher)

        #val['password']=StringVar()                                                 #密码
        #self.label_password["textvariable"] =val['password']
        #val['password'].set(self.password)

        val['call'] = StringVar()                                                   #call
        self.label_call["textvariable"] = val['call']
        val['call'].set(call)

        val['xibie'] = StringVar()                                                  #系别
        self.label_xibie["textvariable"] = val['xibie']
        val['xibie'].set(call)

        val['class_v'] = StringVar()                                                #班级
        self.label_class_v["textvariable"] = val['class_v']
        val['class_v'].set(call)

        val['s_name'] = StringVar()                                                 #姓名
        self.label_s_name["textvariable"] = val['s_name']
        val['s_name'].set(call)


class Application(tk.Frame):

    def __init__(self,master=None):
        tk.Frame.__init__(self,master)
        self.grid()
        self.createWidgets()

    def createWidgets(self):
        self.lblHello=tk.Label(self,text='\n\n\n\n 你好，欢迎进入天朝皇家理工学院课程信息系统！\n\n\n')
        self.lbTitle=tk.Label(self,text='修改个人信息')
        self.lblName=tk.Label(self,text='姓名')
        self.lblaccount=tk.Label(self,text='学号')
        self.lblpassword = tk.Label(self, text='密码')
        self.lblGrade=tk.Label(self,text='年级')
        self.lblxibie=tk.Label(self,text='系别')
        self.lblclass=tk.Label(self,text='班级')
        self.lblFteacher=tk.Label(self,text='辅导员')
        self.lblcall = tk.Label(self, text='call')

        self.lblHello.grid(row=1,column=1)
        self.lblName.grid(row=3,column=0)                   #姓名
        self.lblaccount.grid(row=4,column=0)                #用户名
        self.lblpassword.grid(row=5, column=0)              #密码
        self.lblxibie.grid(row=6,column=0)                  #系别
        self.lblclass.grid(row=7,column=0)                  #班级
        self.lblFteacher.grid(row=8,column=0)               #辅导员
        self.lblcall.grid(row=9, column=0)                  #call


        self.entryName=tk.Entry(self)                      #姓名
        self.entryName.grid(row=3,column=1,columnspan=3)

        self.entryaccount=tk.Entry(self)                   #用户名
        self.entryaccount.grid(row=4,column=1,columnspan=9)

        self.entrypassword = tk.Entry(self)                #密码
        self.entrypassword.grid(row=5, column=1, columnspan=9)

        self.entryclass=tk.Entry(self)                     #班级
        self.entryclass.grid(row=6,column=1,columnspan=3)

        self.entryxibie=tk.Entry(self)                     #系别
        self.entryxibie.grid(row=7,column=1,columnspan=3)

        self.entryFteacher=tk.Entry(self)                  #辅导员
        self.entryFteacher.grid(row=8,column=1,columnspan=3)

        self.entrycall = tk.Entry(self)                    #call
        self.entrycall.grid(row=9, column=1, columnspan=3)

        self.conn = pymysql.Connect(
                host='localhost',
                port=3306,
                user='root',
                passwd='12345',
                db='天朝理工学院',
                charset="utf8"
            )                                              #连接数据库

        self.btnok=tk.Button(self,text='确定修改',command=self.funcok)
        
        self.btnok.grid(row=14,column=1,sticky=tk.E)

    def funcok(self):
        str1=self.entryName.get()                           #姓名
        str2=self.entryaccount.get()                        #用户名
        str3=self.entrypassword.get()                       #密码
        str4 = self.entryclass.get()                        #班级
        str5=self.entryxibie.get()                          #系别
        str6=self.entryFteacher.get()                       #辅导员
        str7= self.entrycall.get()                          #联系方式

        cursor = self.conn.cursor()
        sql = "select password from login where `account`= '%s'" %str2                        #注意包装
        cursor.execute(sql)
        data = cursor.fetchone()
        self.conn.commit()

        if not data:
            messagebox.showwarning(title='警告！', message='账户不存在，请重新输入')
        elif data[0] != str3:
            messagebox.showwarning(title='警告！', message='密码错误，请重新输入')
        else:
            sql = "select * from student where `account`= '%s'" %str2
            cursor.execute(sql)
            data = cursor.fetchone()
            self.conn.commit()

            if not data:
                #sql = "update student set `s-name`='"+str1+"',`class`='"+str4+"',`xibie`='"+str5+"',
                # `f-teacher`='"+str6+"',`call`='"+str7+"' where `account`='"+str2+"'"   #对应的登录账号(修改数据)
                sql ="INSERT INTO student(`s-name`,`class`,`xibie`,`f-teacher`,`call`,`account`) " \
                     "VALUES('%s','%s','%s','%s','%s','%s');" %(str1,str4,str5,str6,str7,str2)
                cursor.execute(sql)
                self.conn.commit()
            else:
                sql = "update student set `s-name`='"+str1+"',`class`='"+str4+"',`xibie`='"+str5+"',`f-teacher`='"+str6+"',`call`='"+str7+"' where `account`='"+str2+"'"   #对应的登录账号(修改数据)
                #sql = r"update student set s-name='%s',class='%s',xibie='%s',f-teacher='%s',
                # call='%s' where account='%s'" % (str1,str4,str5,str6,str7,str2)   #对应的登录账号(修改数据)
                #print(sql)
                cursor.execute(sql)
                self.conn.commit()
            
            cursor.close()
            self.conn.close()
            self.mutex=3
            self.quit()


class Modify_Information_Page():                                                   #窗口4.2修改信息窗口
    def __init__(self):

        root=tk.Tk()
        root.title('皇家理工学院课程信息个人中心')
        root.title("课程管理系统")
        root['width']=300
        root['height']=400
        root.geometry('400x600-100-100')
        app=Application(master=root)
        app.mainloop()

        self.mutex=3
        root.destroy()


class TongGao_Review_Page():                                                        #窗口6通告窗口
    def __init__(self):
        self.conn =  pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                            #连接数据库

        root=Tk()
        root.title("课程管理系统")
        root['width']=300
        root['height']=400
        root['bg']='cyan'
        root.geometry('400x600-100-100')

        f1=Frame(root);f1.pack()
        f2=Frame(root);f2.pack()
        f3=Frame(root);f3.pack()
        f4=Frame(root);f4.pack()

        self.val=StringVar()

        Label(f1,text="欢迎进入！\n  \n",font=('Helvetica',12,'bold'),bg='cyan').pack()
        Label(f2,text="天朝皇家理工学院\n 学生课程内容管理中心\n通告\n",font=('Helvetica',14,'bold'),
              bg='cyan').pack()

        Label(f3,text="#内容",bg='cyan',textvariable=self.val).pack(side=LEFT)

        Button(f4,text="确定",command=self.c1).pack(side=LEFT)
        Button(f4,text="退出",command=root.destroy).pack(side=RIGHT)
        self.mutex=3
        root.mainloop()

    def c1(self):                                                                     #显示通知
        cursor = self.conn.cursor()
        read_sql = "SELECT * FROM inform "
        cursor.execute(read_sql)
        self.conn.commit()
        results=cursor.fetchall()
        self.val.set(results)
        cursor.close()
        self.conn.close()


class Xuanke_waiyulei():                                                             #选课外语类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                             # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（外语类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '     英语1', command=lambda: self.callCheckbutton(course=' 英语1'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '     英语2', command=lambda: self.callCheckbutton(course='英语2'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '     英语3', command=lambda: self.callCheckbutton(course='英语3'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 金融英语', command=lambda: self.callCheckbutton(course='金融英语'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 商务英语', command=lambda: self.callCheckbutton(course='商务英语'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '       日语', command=lambda: self.callCheckbutton(course=' 日语'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '       德语', command=lambda: self.callCheckbutton(course='德语'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)  # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_jisuanjilei():                                     # 选课计算机类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )  # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（计算机类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '     C语言程序设计', command=lambda: self.callCheckbutton(course='C语言程序设计'),
                    bg='pink').pack()

        Checkbutton(self.window, text = 'Java语言程序设计', command=lambda: self.callCheckbutton(course='Java语言程序设计'),
                    bg='pink').pack()

        Checkbutton(self.window, text = 'C++语言程序设计', command=lambda: self.callCheckbutton(course='C++语言程序设计'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '计算机科学与技术', command=lambda: self.callCheckbutton(course='计算机科学与技术'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '            信息工程', command=lambda: self.callCheckbutton(course='信息工程'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '            数据结构', command=lambda: self.callCheckbutton(course='数据结构'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '         数据库原理', command=lambda: self.callCheckbutton(course='数据库原理'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)         # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_guanlilei():                                             # 选课管理类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                            # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（外语类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '    管理学', command=lambda: self.callCheckbutton(course='管理学'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 信息管理', command=lambda: self.callCheckbutton(course='信息管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = ' 财务管理', command=lambda: self.callCheckbutton(course='财务管理'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 风险管理', command=lambda: self.callCheckbutton(course='风险管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = ' 银行管理', command=lambda: self.callCheckbutton(course='银行管理'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)  # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_jingjilei():                                          #选课经济类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                          # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（经济类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = ' 微观经济学', command=lambda: self.callCheckbutton(course='微观经济学'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 宏观经济学', command=lambda: self.callCheckbutton(course='宏观经济学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = ' 风险投资学', command=lambda: self.callCheckbutton(course='风险投资学'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '   财务管理', command=lambda: self.callCheckbutton(course='财务管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = ' 货币银行学', command=lambda: self.callCheckbutton(course='货币银行学'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '   服务管理', command=lambda: self.callCheckbutton(course='服务管理'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)                         #提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_jianzhu():                                                                  #选课建筑窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                                 # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（建筑）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = ' 土木工程', command=lambda: self.callCheckbutton(course='土木工程'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 能源工程', command=lambda: self.callCheckbutton(course='能源工程'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 城市工程', command=lambda: self.callCheckbutton(course='城市工程'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 建筑导论', command=lambda: self.callCheckbutton(course='建筑导论'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)                      # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_fangzhilei():                                                # 选课纺织类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                  # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（纺织类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '       纺织工程', command=lambda: self.callCheckbutton(course='纺织工程'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '       洗漂原理', command=lambda: self.callCheckbutton(course=' 洗漂原理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '       材料导论', command=lambda: self.callCheckbutton(course='材料导论'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 纺织工程导论', command=lambda: self.callCheckbutton(course='纺织工程导论'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '       纺织机械', command=lambda: self.callCheckbutton(course='纺织机械'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)                   #提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_tongshilei():                                                        #选课通识类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                          #连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（通识类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '      思修', command=lambda: self.callCheckbutton(course='思修'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      毛概', command=lambda: self.callCheckbutton(course=' 毛概'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '   近代史', command=lambda: self.callCheckbutton(course='近代史'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '   马克思', command=lambda: self.callCheckbutton(course='马克思'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)           # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_tiyulei():                                                      # 选课体育类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                      #连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（体育类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = ' 羽毛球', command=lambda: self.callCheckbutton(course='羽毛球'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '    篮球', command=lambda: self.callCheckbutton(course=' 篮球'),
                    bg='pink').pack()

        Checkbutton(self.window,text = ' 乒乓球', command=lambda: self.callCheckbutton(course='乒乓球'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '    街舞', command=lambda: self.callCheckbutton(course='街舞'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)        #提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_zhengzhilei():                                             #选课政治类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )  # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（体育类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '       形势与政策', command=lambda: self.callCheckbutton(course='形势与政策'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 马克思主义哲学', command=lambda: self.callCheckbutton(course=' 马克思主义哲学'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)   # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_junshilei():                                                                    # 选课军事类窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                                    # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（体育类）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window, text = '       军训', command=lambda: self.callCheckbutton(course='军训'),
                    bg='pink').pack()

        Checkbutton(self.window, text = ' 国防教育', command=lambda: self.callCheckbutton(course=' 国防教育'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)                   # 提交按钮

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_dayishang():                                         #选课大一上窗口

    def __init__(self,account):
        self.account=account
        self.conn =  pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                          #连接数据库

        self.result=[]
        self.window=Tk()
        self.window.title("课程管理系统-注册")
        self.window['width']=300
        self.window['height']=400
        self.window['bg']='pink'
        self.window.geometry('400x600-100-100')

        f1=Frame(self.window);f1.pack()
        f2=Frame(self.window);f2.pack()

        Label(f1,text="\n欢迎进入！\n \n",font=('Helvetica',12,'bold'),bg='pink').pack()
        Label(f2,text="天朝皇家理工学院\n 学生选课中心\n（大一上）\n    \n",font=('Helvetica',12,'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '一元微积分   ',command = lambda : self.callCheckbutton(course='一元微积分'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '军事理论      ',command = lambda : self.callCheckbutton(course='军事理论'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '思修            ',command = lambda : self.callCheckbutton(course='思修'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '创新创业教育',command = lambda : self.callCheckbutton(course='创新创业教育'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '毛概            ',command = lambda : self.callCheckbutton(course='毛概'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '大学英语      ',command = lambda : self.callCheckbutton(course='大学英语'),
                    bg='pink').pack()

        Checkbutton(self.window,text = 'VB程序设计  ',command = lambda : self.callCheckbutton(course='VB程序设计'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '管理学         ',command = lambda : self.callCheckbutton(course='管理学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '大学语文      ',command = lambda : self.callCheckbutton(course='大学语文'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '形势与政策   ',command = lambda : self.callCheckbutton(course='形势与政策'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '英语听说      ',command = lambda : self.callCheckbutton(course='英语听说'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '羽毛球         ',command = lambda : self.callCheckbutton(course='羽毛球'),
                    bg='pink').pack()

        f3=Frame(self.window);f3.pack()

        Button(f3,text="提交",command=self.c1).pack(side=RIGHT)                  #提交按钮

        self.window.mainloop()


    def callCheckbutton(self,course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute( "SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala=0

        for i in data:
            if i[0] ==self.account:
                lalala=1
        if lalala ==1:
            cursor.execute( "select * from selected where `account`='%s'"% self.account)
            data = cursor.fetchall()
            self.conn.commit()
               
        for i in self.result:
            if (self.account,i) not in data:
                sql ="INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" %(self.account,i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex=3
        self.window.destroy()


class Xuanke_dayixia():                                                      # 大一下选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                      # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大一下）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '   微观经济学', command=lambda: self.callCheckbutton(course='微观经济学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '          毛概2', command=lambda: self.callCheckbutton(course='毛概2'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '   计算机系统', command=lambda: self.callCheckbutton(course='计算机系统'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '            线代', command=lambda: self.callCheckbutton(course='线代'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      大学物理', command=lambda: self.callCheckbutton(course='大学物理'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '         近代史', command=lambda: self.callCheckbutton(course='近代史'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      应用写作', command=lambda: self.callCheckbutton(course='应用写作'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      大学物理', command=lambda: self.callCheckbutton(course='大学物理'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      短式网球', command=lambda: self.callCheckbutton(course='短式网球'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '          C语言', command=lambda: self.callCheckbutton(course=' C语言'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_daershang():                                                     # 大二上选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                      # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大二上）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '     宏观经济学', command=lambda: self.callCheckbutton(course='宏观经济学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '           会计学', command=lambda: self.callCheckbutton(course='会计学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '              篮球', command=lambda: self.callCheckbutton(course='篮球'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '     马克思原理', command=lambda: self.callCheckbutton(course='马克思原理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '           音乐剧', command=lambda: self.callCheckbutton(course='音乐剧'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '        数据结构', command=lambda: self.callCheckbutton(course='数据结构'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '     概率论基础', command=lambda: self.callCheckbutton(course='概率论基础'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '     计算机网络', command=lambda: self.callCheckbutton(course='计算机网络'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '  网络原理基础', command=lambda: self.callCheckbutton(course='网络原理基础'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '        电子商务', command=lambda: self.callCheckbutton(course=' 电子商务'),
                    bg='pink').pack()

        Checkbutton(self.window, text='        网页制作', command=lambda: self.callCheckbutton(course=' 网页制作'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '           金融学', command=lambda: self.callCheckbutton(course=' 金融学'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()

class Xuanke_daerxia():                                                     # 大二下选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                    # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大二下）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text ='         运筹学', command=lambda: self.callCheckbutton(course='运筹学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '        python', command=lambda: self.callCheckbutton(course='python'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '         数据库', command=lambda: self.callCheckbutton(course='数据库'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '创新设计思维', command=lambda: self.callCheckbutton(course='创新设计思维'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '      运作管理', command=lambda: self.callCheckbutton(course='运作管理'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      市场营销', command=lambda: self.callCheckbutton(course='市场营销'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '   组织行为学', command=lambda: self.callCheckbutton(course='组织行为学'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '            IBM', command=lambda: self.callCheckbutton(course='IBM'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      信息检索', command=lambda: self.callCheckbutton(course='信息检索'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '            街舞', command=lambda: self.callCheckbutton(course=' 街舞'),
                    bg='pink').pack()

        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_dasanshang():                                               # 大三上选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                  # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大三上）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '         管理信息系统', command=lambda: self.callCheckbutton(course='管理信息系统'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '               数据仓库', command=lambda: self.callCheckbutton(course='数据仓库'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '企业级软件平台建设', command=lambda: self.callCheckbutton(course='企业级软件平台建设'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '客户关系分析与管理', command=lambda: self.callCheckbutton(course='客户关系分析与管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '         商务智能软件', command=lambda: self.callCheckbutton(course='商务智能软件'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '      数据分析与挖掘', command=lambda: self.callCheckbutton(course='数据分析与挖掘'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '            数理统计学', command=lambda: self.callCheckbutton(course='数理统计学'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_dasanxia():                                                       # 大三下选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                        # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大三下）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '         决策支持系统导论', command=lambda: self.callCheckbutton(course='决策支持系统导论'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '      信息系统分析与设计', command=lambda: self.callCheckbutton(course='信息系统分析与设计'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '               企业资源计划', command=lambda: self.callCheckbutton(course='企业资源计划'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '                     财务管理', command=lambda: self.callCheckbutton(course='财务管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '                     机器学习', command=lambda: self.callCheckbutton(course='机器学习'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '         物流和供应链管理', command=lambda: self.callCheckbutton(course='物流和供应链管理'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '                  IT项目管理', command=lambda: self.callCheckbutton(course='IT项目管理'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '            计算机信息安全', command=lambda: self.callCheckbutton(course='计算机信息安全'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '               信息系统评价', command=lambda: self.callCheckbutton(course='信息系统评价'),
                    bg='pink').pack()

        Checkbutton(self.window, text = '管理系统业务流程的仿真', command=lambda: self.callCheckbutton(course='管理系统业务流程的仿真'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_dasishang():                                                    # 大四上选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                      # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大四上）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = '         成本与管理会计', command=lambda: self.callCheckbutton(course='成本与管理会计'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '大数据技术原理与应用', command=lambda: self.callCheckbutton(course='大数据技术原理与应用'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '            信息系统审计', command=lambda: self.callCheckbutton(course='信息系统审计'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '   人工智能与专家系统', command=lambda: self.callCheckbutton(course=' 人工智能与专家系统'),
                    bg='pink').pack()

        Checkbutton(self.window,text = '                  专业实习', command=lambda: self.callCheckbutton(course='专业实习'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()


class Xuanke_dasixia():                                                   # 大四下选课窗口

    def __init__(self, account):
        self.account = account
        self.conn = pymysql.Connect(
            host='localhost',
            port=3306,
            user='root',
            passwd='12345',
            db='天朝理工学院',
            charset="utf8"
        )                                                                   # 连接数据库

        self.result = []
        self.window = Tk()
        self.window.title("课程管理系统-注册")
        self.window['width'] = 300
        self.window['height'] = 400
        self.window['bg'] = 'pink'
        self.window.geometry('400x600-100-100')

        f1 = Frame(self.window);
        f1.pack()
        f2 = Frame(self.window);
        f2.pack()

        Label(f1, text="\n欢迎进入！\n \n", font=('Helvetica', 12, 'bold'), bg='pink').pack()
        Label(f2, text="天朝皇家理工学院\n 学生选课中心\n（大四下）\n    \n", font=('Helvetica', 12, 'bold'),
              bg='pink').pack()

        Checkbutton(self.window,text = ' 毕业论文（不选我你就死定了！）', command=lambda: self.callCheckbutton(course='毕业论文'),
                    bg='pink').pack()


        f3 = Frame(self.window);
        f3.pack()

        Button(f3, text="提交", command=self.c1).pack(side=RIGHT)

        self.window.mainloop()

    def callCheckbutton(self, course):
        self.result.append(course)

    def c1(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM selected ")
        self.conn.commit()
        data = cursor.fetchall()
        lalala = 0

        for i in data:
            if i[0] == self.account:
                lalala = 1
        if lalala == 1:
            cursor.execute("select * from selected where `account`='%s'" % self.account)
            data = cursor.fetchall()
            self.conn.commit()

        for i in self.result:
            if (self.account, i) not in data:
                sql = "INSERT INTO selected(`account`,`course`) VALUES('%s','%s');" % (self.account, i)
                cursor.execute(sql)
                self.conn.commit()

        cursor.close()
        self.conn.close()
        self.mutex = 3
        self.window.destroy()



if __name__ == '__main__':
    mutex=1
    while(1):
        if mutex==1:                                                 #窗口1为首页
            homepage=HomePage()
            mutex=homepage.mutex
            continue

        elif mutex==2:                                              #窗口2为注册页面
            registerPage=RegisterPage()
            mutex=registerPage.mutex
            continue

        elif mutex==3:                                              #窗口3为operation界面
            mutex=OperationPage().mutex
            continue

        elif mutex==4.1:                                            #窗口4.1为个人信息查看窗口
            mutex=Person_Information_Review_Page(homepage.account,homepage.password).mutex
            continue

        elif mutex==4.2:                                            #窗口4.2为修改信息窗口
            mutex=Modify_Information_Page().mutex
            continue

        elif mutex==6:                                              #窗口6为通告窗口
            mutex=TongGao_Review_Page().mutex


        elif mutex==7.11 :                                          #窗口7.1.1为选课，大一上窗口
            mutex=Xuanke_dayishang(homepage.account).mutex

        elif mutex==7.12 :                                          #窗口7.1.2为选课，大一下窗口
            mutex=Xuanke_dayixia(homepage.account).mutex

        elif mutex == 7.21:                                         # 窗口7.21为选课，大二上窗口
            mutex = Xuanke_daershang(homepage.account).mutex

        elif mutex == 7.22:                                         # 窗口7.22为选课，大二下窗口
            mutex = Xuanke_daerxia(homepage.account).mutex

        elif mutex == 7.31:                                         # 窗口7.31为选课，大三上窗口
            mutex = Xuanke_dasanshang(homepage.account).mutex

        elif mutex == 7.32:                                         # 窗口7.32为选课，大三下窗口
            mutex = Xuanke_dasanxia(homepage.account).mutex

        elif mutex == 7.41:                                         # 窗口7.41为选课，大四上窗口
            mutex = Xuanke_dasishang(homepage.account).mutex

        elif mutex == 7.42:                                         # 窗口7.42为选课，大四下窗口
            mutex = Xuanke_dasixia(homepage.account).mutex



        elif mutex == 8.1:  # 窗口8.1为选课，计算机类窗口
            mutex = Xuanke_jisuanjilei(homepage.account).mutex

        elif mutex == 8.2:  # 窗口8.2为选课，外语类窗口
            mutex = Xuanke_waiyulei(homepage.account).mutex

        elif mutex == 8.3:  # 窗口8.3为选课，管理类窗口
            mutex = Xuanke_guanlilei(homepage.account).mutex

        elif mutex == 8.4:  # 窗口8.4为选课，经济类窗口
            mutex = Xuanke_jingjilei(homepage.account).mutex

        elif mutex == 8.5:  # 窗口8.5为选课，建筑窗口
            mutex = Xuanke_jianzhu(homepage.account).mutex

        elif mutex == 8.6:  # 窗口8.6为选课，纺织类窗口
            mutex = Xuanke_fangzhilei(homepage.account).mutex

        elif mutex == 8.7:  # 窗口8.7为选课，通识类窗口
            mutex = Xuanke_tongshilei(homepage.account).mutex

        elif mutex == 8.8:  # 窗口8.8为选课，体育类窗口
            mutex = Xuanke_tiyulei(homepage.account).mutex

        elif mutex == 8.9:  # 窗口8.9为选课，政治类窗口
            mutex = Xuanke_zhengzhilei(homepage.account).mutex

        elif mutex == 8.10:  # 窗口8.9为选课，军事类窗口
            mutex = Xuanke_junshilei(homepage.account).mutex


        elif mutex == 6.1:  # 窗口6.1为课程信息，所选课程窗口
            mutex = suoxuankecheng(homepage.account).mutex

        elif mutex == 6.2:  # 窗口6.2为课程信息，教师信息介绍窗口
            mutex = jiaoshixinxijieshao(homepage.account).mutex

        elif mutex == 6.3:  # 窗口6.3为课程信息，课程介绍窗口
            mutex = kechengjieshao(homepage.account).mutex


        else:               #结束
            break
