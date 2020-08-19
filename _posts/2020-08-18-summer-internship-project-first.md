---
title: 2020暑期实训项目一流程简介
date:  2020-08-18 15:28:20 +0800
category: 后端
tags: Python
excerpt: Python-Django项目
---


# 2020暑期实训项目一流程简介

+ Python
+ Django
+ HTML, JavaScript, BootStrap

## 前端：

+ 搭建html页面
    + index.html引入jquery，bootstrap等需要的库，如果不使用css框架可以不用引入bootstrap

```
<!DOCTYPE html>
{% load staticfiles %}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SDN-QoS</title>
    <link href="{% static 'css/bootstrap.min.css' %}" rel="stylesheet">
</head>
<body>
<div class="container-fluid">

    <!-- 添加流表部分 -->
    <div class="alert alert-primary" role="alert">
        <h3>添加流表</h3>
    </div>
    <form action="POST" id="addFlow">

        <!-- 基本信息部分 -->
        <nav class="navbar navbar-light" style="background-color: #e3f2fd;">
            <span class="navbar-text">基本信息</span>
        </nav>
        <div class="form-group">
            <span>Switch</span>
            <input type="text" class="form-control" name="switch_id" id="switch_id" placeholder="input switch ID">
        </div>
        <div class="form-group">
            <span>Name</span>
            <input type="text" class="form-control" name="name" id="name" placeholder="input name">
        </div>
        <div class="form-group">
            <span>Priority</span>
            <input type="text" class="form-control" name="priority" id="priority" placeholder="input priority">
        </div>
        <hr>
        <!-- 匹配字段部分 -->
        <nav class="navbar navbar-light" style="background-color: #e3f2fd;">
            <span class="navbar-text">匹配字段</span>
        </nav>
        <div class="form-group">
            <span>in-port</span>
            <input type="text" class="form-control" name="in_port" id="in_port" placeholder="input in_port">
        </div>
        <div class="form-group">
            <span>Ethernet</span>
            <input type="text" class="form-control" name="ethernet" id="ethernet" placeholder="input Ethernet">
        </div>
        <div class="form-group">
            <span>IP-source</span>
            <input type="text" class="form-control" name="ip_source" id="ip_source" placeholder="input IP-source">
        </div>
        <div class="form-group">
            <span>IP-destination</span>
            <input type="text" class="form-control" name="ip_dest" id="ip_dest" placeholder="IP-destination">
        </div>
        <div class="form-row">
            <div class="col">
                <span>layer4-match</span>
                <select class="form-control" name="layer4_match" id="layer4_match">
                    <option>TCP</option>
                    <option>UDP</option>
                </select>
            </div>
            <div class="col">
                <span>源端口</span>
                <input type="text" class="form-control" name="source_port" id="source_port">
            </div>
            <div class="col">
                <span>目的端口</span>
                <input type="text" class="form-control" name="dest_port" id="dest_port">
            </div>
        </div>

       <hr>
        
        <!-- 动作选择部分 -->
        <nav class="navbar navbar-light" style="background-color: #e3f2fd;">
            <span class="navbar-text">动作</span>
        </nav>
        <div class="form-row">
            <div class="col">
                <span>action</span>
                <select class="form-control" name="action" id="action">
                    <option>output</option>
                    <option>drop</option>
                </select>
            </div>
            <div class="col">
                <span>out-port</span>
                <input type="text" class="form-control" name="out_port" id="out_port">
            </div>
        </div>
        <hr>
        <button type="button" id="add_flow" class="btn btn-outline-primary btn-block">添加</button>
    </form>

    <hr>
    <div class="alert alert-primary" role="alert">
        <h3>流表管理</h3>
    </div>

    <button type="button" id="del_flow" class="btn btn-outline-primary btn-block">删除所选流表</button>
    <hr>
    <table class="table table-hover">
        <thead class="thead-dark">
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Priority</th>
            <th scope="col">Match</th>
            <th scope="col">Instructions</th>
          </tr>
        </thead>
        <tbody>
	{% for item in table %}
          <tr>
            <th scope="row">
                <div class="custom-control custom-switch">
                    <input type="checkbox" class="custom-control-input" name="switch_id" id='{{forloop.counter}}' value="{{item.flow_node}}">
                    <label class="custom-control-label" for="{{forloop.counter}}">{{item.flow}}</label>
                </div>
            </th>
            <td>{{item.priority}}</td>
            <td>{{item.match}}</td>
            <td>{{item.instruction}}</td>
          </tr>
	{% endfor %}
        </tbody>
      </table>
</div>
<script src="/static/js/jquery-3.3.1.min.js"></script>
<script src="/static/js/bootstrap.min.js"></script>
<script src="/static/js/index.js"></script>
</body>
</html>

```

+ index.js
    + 使用jQuery获取表单内容
    + 简单处理数据，使用ajax传递给后端

```javascript
// 检查action的值
function checkAction(action){
    if(action == 'drop'){
        $('#out_port').css("display", "none");
    }else {
        $('#out_port').css("display", "");
    }
}

// 给action select添加change事件
$('#action').change(function (e) { 
    e.preventDefault();
    let action = $('#action').val();
    checkAction(action);
});

// 序列化表单数据，生成json格式的数据
$('#add_flow').click(function (e) {
    e.preventDefault();
    let data = {};
    let value = $('#addFlow').serializeArray();
    $.each(value, function (index, item) {
                data[item.name] = item.value;
            });
    let json = JSON.stringify(data);
    console.log('json : ',json);
    $.ajax({
    	type: "POST",
	url: "add-flow/",
	'Content-Type': 'application/json',
	datatype: 'json',
	data: json,
	success: function(data){
	    if(data.result == "success"){
	    	alert('AddFLow success!');
	    }
	}
    });    
});

var items = [];
$('#del_flow').click(function(e){
    e.preventDefault();
    items.splice(0,items.length);
    $("input[type='checkbox'][name='switch_id']").each(function(){
    	if($(this).is(":checked")){
	    items.push($(this).val());
	}
    });
    console.log('items:', items)
    $.ajax({
	type: "POST",
	url: "del-flow/",
	'Content-Type': 'application/json',
	datatype: 'json',
	data: JSON.stringify(items),
	success: function(data){
	    if(data.result == 'success'){
	        alert('DeleteFlow success!')
	    }
	}
    });
});
```

## 后端：

+ python创建django工程

```python
django-admin startproject xxx
cd xxx
django-admin startapp xxxx
```

+ 修改项目配置文件settings.py

```python
settings.py:
...
ALLOWED_HOSTS = ['*']

#添加APP到安装列表中
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'QosApp',
]

#注释csrf这一行解决跨域问题
MIDDLEWARE_CLASSES = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    #'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

+ 修改urls.py

```python
from django.conf.urls import url
from django.contrib import admin
from QosApp import views

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^add-flow/', views.CreateFlow),
]
```

+ 进入app目录修改views

```python
import json
from django.shortcuts import render
from django.http import JsonResponse
from django.core import serializers
from .models import Table

from .odl import add_flow, flow_delete

# Create your views here.

def CreateFlow(request):
    if(request.method == 'GET'):
	context = {}
	res = Table.objects.all()
	context['table'] = res
	return render(request, 'index.html', context)
    elif(request.method == 'POST'):
	rec_data = json.loads(request.body)
	print(rec_data)
	flow_info = {
	    'switch_id': rec_data.get('switch_id'),
	    'name': rec_data.get('name'),
	    'priority': rec_data.get('priority'),
	}
	match = {
	    'in_port': rec_data.get('in_port'),
	    'ethernet': rec_data.get('ethernet'),
	    'ip_source': rec_data.get('ip_source'),
	    'ip_dest': rec_data.get('ip_dest'),
	    'layer4_match': rec_data.get('layer4_match'),
	    'source_port': rec_data.get('source_port'),
	    'dest_port': rec_data.get('dest_port'),
	}
	action = {
	    'action': rec_data.get('action'),
	    'out_port': rec_data.get('out_port'),
	}
	add_flow(flow_info, match, action)
	res = {'result': 'success'}
	return JsonResponse(res)
```

+ 定义models.py

```python
from __future__ import unicode_literals

from django.db import models

# Create your models here.

class Table(models.Model):
    id = models.AutoField(primary_key=True)
    flow = models.CharField(default='', max_length=512)
    flow_node = models.CharField(default='', max_length=256)
    priority = models.CharField(default='', max_length=128)
    match = models.CharField(max_length=1024)
    instruction = models.CharField(max_length=1024)
```

+ 修改odl.py 的add_flow函数
 
```python
def add_flow(flow_info, match, action):
    
    switch_id = flow_info['switch_id']
    name = flow_info['name']
    priority = flow_info['priority']
    in_port = match['in_port']
    url = "/restconf/config/opendaylight-inventory:nodes/node/" + switch_id + "/flow-node-inventory:table/0/flow/" + in_port
    flow_set = {
    'id': in_port,
    'flow-name': name,
    'table_id': 0,
    'priority': priority,
    }

    #match
    match_set = {
	"ethernet-match": {
	    "ethernet-type":{"type": match['ethernet']}
	},
	"ipv4-source": match['ip_source'],
	"ipv4-destination": match['ip_dest'],
	"ip-match": {"ip-protocol": 6},
	"tcp-destination-port": match["dest_port"],
    }

    #action
    action_set = {
       'order': '0',
        'output-action':{
	    "output-node-connector": switch_id +':' + action['out_port'],
	    "max-length": 65535,
    	}
    }

    instruc_set = {
        "instruction": [{
            "order": '0',
            "apply-actions": {
                "action": [action_set]
            }
        }]
    }

    flow_set['match'] = match_set
    flow_set['instructions'] = instruc_set
    table = Table(flow=name, flow_node=switch_id, priority=priority, match=match_set, instruction=instruc_set)
    table.save()
    body = json.dumps({"flow":flow_set})
    pre_put(url, body)
```

## 静态资源配置

+ 在app目录下面创建templates目录，把html页面放进去
+ 在app目录下面创建static目录
    + 在static目录下面创建css, js, img等目录, 把js文件放进对应的目录中


## 数据库连接

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'QosSite',
	'USER': 'root',
	'PASSWORD': '****',
	'HOST': '127.0.0.1',
	'PORT': '3306',
    }
}
```

## 数据模型迁移命令

```python
#在manage.py所在的目录下面执行

python manage.py makemigrations
python manage.py migrate

```

## 执行程序命令

```python
python mange.py runserver
# 打开你的url
```