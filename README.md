
import sqlite3
import time
import datetime


conn = sqlite3.connect('test.db')

c = conn.cursor()
# c.execute('''CREATE TABLE Automatic
#        (happenTime INT PRIMARY KEY     NOT NULL,
#        MATTER           TEXT    NOT NULL,
#        TOTAL            INT     NOT NULL,
#        TODAY        INT);''')
# print ("Table created successfully")
# conn.commit()

# c.execute("INSERT INTO Automatic (happenTime,matter,total,today) \
#       VALUES (0, '开始', 0, 0)")

lastTotal = 0
lastToday = 0
# 查找最新的一条数据
# cursor = c.execute("SELECT max(happenTime) from Automatic")
# for row in cursor:
#     print(row[0])
#     cursor1 = c.execute("SELECT * from Automatic where happenTime = "+str(row[0]))
#     for row1 in cursor1:
#         lastTotal = row1[2]
#         lastToday = row1[3]

# print(lastTotal)
# print(lastToday)
# t = time.time()
# print(int(t))
# 小坑，如何不提交的话，数据库并不会记录这条数据，只能使用一次
# conn.commit()
# cursor = c.execute("SELECT happenTime, matter, total, today  from Automatic")
# for row in cursor:
#    print ("happenTime = ", row[0])
#    print ("matter = ", row[1])
#    print ("total = ", row[2])
#    print ("today = ", row[3], "\n")

# c.execute("drop table COMPANY")
# conn.commit()


from tkinter import *
import tkinter  
from  tkinter  import ttk  
root = Tk()
# https://www.cnblogs.com/zhangpengshou/p/3626137.html
def getTimeOClockOfToday():
    import time
    t = time.localtime(time.time())
    time1 = time.mktime(time.strptime(time.strftime('%Y-%m-%d 00:00:00', t),'%Y-%m-%d %H:%M:%S'))
    return time1

def getTimeEndToday():
    import time
    t = time.localtime(time.time())
    time1 = time.mktime(time.strptime(time.strftime('%Y-%m-%d 23:59:59', t),'%Y-%m-%d %H:%M:%S'))
    return time1


def quit():
    conn.close()
#按扭调用的函数，
def reg():
    lastTotal = 0
    lastToday = 0
    # 查找最新的一条数据
    cursor = c.execute("SELECT max(happenTime) from Automatic")
    for row in cursor:
        cursor1 = c.execute("SELECT * from Automatic where happenTime = "+str(row[0]))
        for row1 in cursor1:
            lastTotal = row1[2]
            lastToday = row1[3]
    matter = e_user.get()
    t = time.time()
    happenTime = int(t)
    total = lastTotal + int(comboxlist.get())
    today = int(comboxlist.get())
    startTime = getTimeOClockOfToday()
    endTime = getTimeEndToday()
    print('开始时间',startTime)
    print('结束时间',endTime)
    if matter != '':
        c.execute("INSERT INTO Automatic (happenTime,matter,total,today) \
          VALUES ('"+str(happenTime)+"', '"+str(matter)+"', '"+str(total)+"', '"+str(today)+"')")
        conn.commit()

    cursor1 = c.execute("SELECT today from Automatic where happenTime < "+str(int(endTime))+" and happenTime >"+str(int(startTime)))
    totalDay = 0   
    for row1 in cursor1:
        totalDay = totalDay +row1[0]

    cursor = c.execute("SELECT * from Automatic")
    for row in cursor:
        print('时间',row[0])
        print('事件',row[1])
        print('总分',row[2])
        print('今日',row[3])
    total1.delete(0,END)
    total1.insert(0,str(total))
    today1.delete(0,END)
    today1.insert(0,str(totalDay))


def regUse():
    cursor = c.execute("SELECT max(happenTime) from Automatic")
    use = int(comboxlistUse.get())
    for row in cursor:
        cursor1 = c.execute("update  Automatic set total = total -"+str(use)+" where happenTime = "+str(row[0]))
    cursor1 = c.execute("select total from Automatic  where happenTime = "+str(row[0]))
    total = 0
    for row in cursor:
        total = row[0]

    total1.delete(0,END)
    total1.insert(0,str(total))

def go(*args):   #处理事件，*args表示可变参数  
    print(comboxlist.get()) #打印选中的值 



cursor = c.execute("SELECT max(happenTime) from Automatic")
for row in cursor:
    cursor1 = c.execute("SELECT * from Automatic where happenTime = "+str(row[0]))
    for row1 in cursor1:
        lastTotal = row1[2]
        lastToday = row1[3] 
startTime = getTimeOClockOfToday()
endTime = getTimeEndToday()
cursor1 = c.execute("SELECT today from Automatic where happenTime < "+str(int(endTime))+" and happenTime >"+str(int(startTime)))
totalDay = 0   
for row1 in cursor1:
    totalDay = totalDay +row1[0]
 
#第一行，用户名标签及输入框
l_user =Label(root,text='事件：')
l_user.grid(row=0,sticky=W)
e_user =Entry(root,width = 30)
e_user.grid(row=0,column=1,sticky=E)
 
# 第二行，密码标签及输入框
l_pwd = Label(root,text='添加分值：')
l_pwd.grid(row=1,sticky=W)
l_pwdUse = Label(root,text='使用分值：')
l_pwdUse.grid(row=2,sticky=W)

l_pwdTotal = Label(root,text='总分：')
l_pwdTotal.grid(row=4,sticky=W)

l_pwdToday = Label(root,text='今日分数：')
l_pwdToday.grid(row=5,sticky=W)

default_value = StringVar()
default_value.set(lastTotal)
total1 =Entry(root,textvariable = default_value,width = 8)
total1.grid(row=4,column=1,sticky=W)

default_value1 = StringVar()
default_value1.set(totalDay)
today1 =Entry(root,textvariable = default_value1,width = 8)
today1.grid(row=5,column=1,sticky=W)
 
#第三行登陆按扭，command绑定事件
b_login = Button(root,text='添加',command=reg)
b_login.grid(row=3,column=1,sticky=E)

b_login2 = Button(root,text='使用',command=regUse)
b_login2.grid(row=3,column=2,sticky=E)

comvalue=tkinter.StringVar()#窗体自带的文本，新建一个值  
comboxlist=ttk.Combobox(root,textvariable=comvalue) #初始化  
comboxlist["values"]=("1","2","3","4")  
comboxlist.current(0)  #选择第一个  
comboxlist.bind("<<ComboboxSelected>>",go)  #绑定事件,(下拉列表框被选中时，绑定go()函数)  
comboxlist.grid(row=1,column=1,sticky=W)


comvalueUse=tkinter.StringVar()#窗体自带的文本，新建一个值  
comboxlistUse=ttk.Combobox(root,textvariable=comvalueUse) #初始化  
comboxlistUse["values"]=("1","2","3","4")  
comboxlistUse.current(0)  #选择第一个  
comboxlistUse.bind("<<ComboboxSelected>>",go)  #绑定事件,(下拉列表框被选中时，绑定go()函数)  
comboxlistUse.grid(row=2,column=1,sticky=W)


 
#登陆是否成功提示
l_msg = Label(root,text='')
l_msg.grid(row=3)

root.wm_attributes('-topmost',1)
 
root.mainloop()
