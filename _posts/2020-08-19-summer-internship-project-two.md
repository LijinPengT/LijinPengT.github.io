---
title: 2020暑期实训项目二流程简介
date:  2020-08-19 23:28:20 +0800
category: 后端
tags: Python
excerpt: Meter表下发及管理
---

# 2020暑期实训项目二

## 项目功能

+ 前端系统页面
    + 输入表的数据
    + 执行(下发流表，下发meter表)成功/提示成功，并刷新页面
    + 执行失败/提示失败，不刷新
    + 删除选中的表，并实时刷新页面，提示删除成功

+ 交换机显示
    + 下发成功，显示流表和meter表
    + 下发失败，不显示


## 核心代码展示

### 配置文件设置

+ settings.py

```python
***
ALLOWED_HOSTS = ['*']
# 将app的名字添加到INSTALLED_APPS列表的末尾
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'QosApp', # 自己创建的app名字
]

# 注释csrf这一行，解决跨域
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
***
# 配置数据库
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '数据库名字',
	'USER': 'root',
	'PASSWORD': '数据库密码',
	'HOST': '127.0.0.1',
	'PORT': '3306',
    }
}
```

### 前端

#### index.html(省去了head)

```html
<body>
<div class="container-fluid">

    <!-- 添加meter表 -->
    <div class="alert alert-primary" role="alert">
        <h3>添加Meter表</h3>
    </div>
    <form action="POST" id="addMeter">
        <div class="form-group">
            <span>MeterID</span>
            <input type="text" class="form-control" name="meter_id" id="meter_id" placeholder="input Meter ID">
        </div>
        <div class="form-group">
            <span>MeterType</span>
            <select class="form-control" name="meter_type" id="meter_type">
                <option>ofpmbt-drop</option>
                <option>output</option>
            </select>
        </div>
        <div class="form-group">
            <span>BandRate</span>
            <input type="text" class="form-control" name="band_rate" id="band_rate" placeholder="input band rate">
        </div>
        <div class="form-group">
            <span>BandSize</span>
            <input type="text" class="form-control" name="band_size" id="band_size" placeholder="input band size">
        </div>
        <hr>
        <button type="button" id="add_meter" class="btn btn-outline-primary btn-block">添加</button>
    </form>

    <hr>
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
            <input type="text" class="form-control" name="swicth_id" id="swicth_id" placeholder="input switch ID">
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
                <span>MeterID</span>
                <input type="text" class="form-control" name="meter_id" id="meterid">
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
            <th scope="col">Name</th>
            <th scope="col">SwitchID</th>
            <th scope="col">Priority</th>
            <th scope="col">Match</th>
            <th scope="col">Instructions</th>
          </tr>
        </thead>
        <tbody>
          <!-- 别忘了for循环 -->
          <tr>
            <th scope="row">
                <div class="custom-control custom-switch">
                    <input type="checkbox" class="custom-control-input" name="in_port" id="{{forloop.counter}}" value="{{item.inport}}">
                    <label class="custom-control-label" for="{{forloop.counter}}">{{item.flow}}</label>
                </div>
            </th>
            <td>{{item.flow_node}}</td>
            <td>{{item.priority}}</td>
            <td>{{item.match}}</td>
            <td>{{item.instruction}}</td>
          </tr>

        </tbody>
      </table>

      <!-- Meter表管理 -->
      <hr>
    <div class="alert alert-primary" role="alert">
        <h3>Meter表管理</h3>
    </div>
    <button type="button" id="del_meter" class="btn btn-outline-primary btn-block">删除所选Meter表</button>
    <hr>
    <table class="table table-hover">
        <thead class="thead-dark">
          <tr>
            <th scope="col">MeterID</th>
            <th scope="col">Type</th>
            <th scope="col">Rate(Kbps)</th>
            <th scope="col">burst_size(Kbps)</th>
          </tr>
        </thead>
        <tbody>
            <!-- 别忘了for循环 -->
          <tr>
            <th scope="row">
                <div class="custom-control custom-switch">
                    <input type="checkbox" class="custom-control-input" name="meter_id" id="'meter' + {{meter.meter}}" value="{{meter.meter}}">
                    <label class="custom-control-label" for="'meter' + {{meter.meter}}">{{meter.meter}}</label>
                </div>
            </th>
            <td>{{meter.meterType}}</td>
            <td>{{meter.bandRate}}</td>
            <td>{{meter.banSize}}</td>
          </tr>
        </tbody>
      </table>
</div>
<script src="static/js/jquery-3.3.1.min.js"></script>
<script src="static/js/bootstrap.min.js"></script>
<script src="static/js/index.js"></script>
</body>
```

#### index.js(对比项目一的js代码进行了一些优化)

```javascript
// 检查action的值
function checkAction(action){
    // 如果action选择的是drop，就不显示out_port元素
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
    
    // 获取addFlow表单的数据，并序列化为数组
    let value = $('#addFlow').serializeArray();
    
    // 对数组中的每个数据取其名字和值，存储在data对象中
    $.each(value, function (index, item) {
                data[item.name] = item.value;
            });

    // 将data对象转换成JSON格式
    let json = JSON.stringify(data);
    console.log('json : ',json);

    // ajax POST请求
    $.ajax({
    	type: "POST",
        url: "add-flow/",
        'Content-Type': 'application/json',
        datatype: 'json',
        data: json,
        // 请求成功接收后端返回值
        success: function(data){
            if(data.result == "success"){
                //如果是success则提示成功并刷新页面
                alert('AddFLow success!');
                location.reload();
            }else{
                alert('AddFlow Failed!')；
            }
        }
    });    
});

// 删除流表
var items = [];
$('#del_flow').click(function(e){
    e.preventDefault();

    //清空items[]数组
    items.splice(0,items.length);

    //判断获取到的每一个name='in_port'的checkbox
    //如果是选中状态则将其值添加到items数组中
    $("input[type='checkbox'][name='in_port']").each(function(){
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
        
        //将items数组转化成json格式，传给后端
        data: JSON.stringify(items),
        success: function(data){
            if(data.result == 'success'){
                alert('DeleteFlow success!');
                location.reload();
            }else{
                alert('DeleteFlow Failed!');
            }
        }
    });
});

// 添加meter表，类似添加流表的操作
$('#add_meter').click(function(e){
    e.preventDefault();
    let data = {}
    let value = $('#addMeter').serializeArray();
    $.each(value, function(index, item){
    	data[item.name] = item.value;
    });
    let json = JSON.stringify(data);
    console.log('JSON: ', json);
    $.ajax({
	    type: "POST",
	    url: "add-meter/",
	    'Content-Type': 'application/json',
        datatype: 'json',
        data: json,
        success: function(data){
            if(data.result == 'success'){
                alert('AddMeter success!');
		        location.reload();
            }else{
                alert('AddMeter Failed!');
            }
        }

    });
});

//delete-meter，类似删除流表的操作
var meters = [];
$('#del_meter').click(function(e){
    e.preventDefault();
    meters.splice(0, meters.length);
    $("input[type='checkbox'][name='meter_id']").each(function(){
	if($(this).is(":checked")){
	    meters.push($(this).val());
	}
    });
    console.log(meters);
    $.ajax({
        type: "POST",
        url: "del-meter/",
        'Content-Type': 'application/json',
        datatype: 'json',
        data: JSON.stringify(meters),
        success: function(data){
            if(data.result == 'success'){
                alert('DeleteMeter success!');
		        location.reload();
            }else{
                alert('DeleteMeter Failed!');
            }
        }
    });
});
```

+ urls.py QosApp/urls.py

```python
# QosSite/urls.py
from django.conf.urls import url, include
from django.contrib import admin
urlpatterns = [
    url(r'^admin/', admin.site.urls),

    #引入QosApp的urls
    url(r'', include('QosApp.urls')),
]

# QosApp/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.Index),
    url(r'^add-flow/', views.CreateFlow),
    url(r'^add-meter/', views.CreateMeter),
    url(r'^del-flow/', views.DelFlow),
    url(r'^del-meter/', views.DelMeter),
]
```

+ views.py(对比项目一，对数据的接收与处理和函数执行返回值的处理进行了优化)

```python
import json
from django.shortcuts import render, redirect
from django.http import JsonResponse
from django.core import serializers
from .models import Table, Meter

from .odl import add_flow, flow_delete, add_meter, meter_delete

# Create your views here.

#设置Index函数，渲染首页
def Index(request):
    context = {}

    # 获取数据库中的tables并根据priority排序
    tables = Table.objects.all().order_by('priority')
    meters = Meter.objects.all()
    context = {
    	'table': tables,
	    'meters': meters,
    }
    return render(request, 'index.html',context)

# 下发流表
def CreateFlow(request):
    if(request.method == 'POST'):
        #json.loads()解析
	    rec_data = json.loads(request.body)
	    print(rec_data)

        # 将前端获取到的数据，分别封装到下面三个字典中
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
            'meter_id': rec_data.get('meter_id'),
            'out_port': rec_data.get('out_port'),
        }

        # 调用odl.py的add_flow函数，并获取其返回值
        can_add = add_flow(flow_info, match, action)
        
        # res字典为返回给前端的返回值
        res = {}
        print(can_add['result'])
        
        #如果add_flow成功则返回success，否则返回failed
        if(can_add['result'] == 'add success'):
            res['result']= 'success'
        else:
            res['result'] = 'failed'
        return JsonResponse(res)
    else:
	    return render(request, 'index.html', {})

# 下发meter表
def CreateMeter(request):
    if(request.method == 'POST'):
        req_data = json.loads(request.body)
        print(req_data)

        #将数据封装近meter字典中
        meter = {
            'meter_id': req_data.get('meter_id'),
            'meter_type': req_data.get('meter_type'),
            'band_rate': req_data.get('band_rate'),
            'band_size': req_data.get('band_size'),
        }

        # 调用odl.py的add_meter函数，并获取其返回值
        can_add = add_meter(meter)
        print(can_add['result'])
        
        res = {}
        if(can_add['result'] == 'add success'):
            res['result'] = 'success'
        else:
            res['result'] = 'failed'
        return JsonResponse(res)
    else:
	    return render(request, 'index.html', {})


# 删除流表
def DelFlow(request):
    if(request.method=='POST'):
        res_data = request.body
        print(res_data)
        
        # 因为下发流表时，使用in_port作为其id，所以前端获取in_port值，遍历传回来的id值
        for port in res_data:
            print('delete ID: ', port)

            # 通过filter()查询inport=port的table，并删除
            flow = Table.objects.filter(inport=port)
            flow.delete()

            # 调用odl.py的flow_delete函数，并获取返回值
            del_res = flow_delete(port)
            if(del_res['result'] == 'success'):
                res = {'result': 'success'}
            else:
                res['result'] = 'failed'
            return JsonResponse(res)
    else:
	    return render(request, 'index.html', {})


# 删除meter函数
def DelMeter(request):
    if(request.method == 'POST'):

        #前端获取meter_id列表
        req_data = request.body
        print(req_data)
        res_list = []
        for meter_id in req_data:
            meter = Meter.objects.filter(meter=meter_id)
            meter.delete()
            del_res = meter_delete(meter_id)

            #将调用meter_delete函数的返回结果存储在res_list之中
            res_list.append(del_res['result'])
        print(res_list)
        res = {'result': 'success'}
        return JsonResponse(res)
    else:
	    return render(request, 'index.html', {})

```

+ odl.py(对流表，meter表的删除和添加进行了优化，返回值进行了优化)

```python
import json
import base64
import httplib
from .models import Table, Meter

# 本地开发，switch_id是唯一的，所以设置switch_id为全局变量
switch_id="openflow:xxxx"

# 请求下发函数
def pre_put(url, body):
    try:
        auth = base64.b64encode('admin:admin'.encode())
        headers = {"Authorization": "Basic " + auth, "Content-Type": "application/json"}
        conn = httplib.HTTPConnection('127.0.0.1:8181', timeout=3)    
        conn.request("PUT", url, body, headers)
        response = conn.getresponse()
        
        if response.status in [200,201]:
            return {'result': 'add success'}
        else:
            return {'result': 'add failure'}
   
    except Exception as e:
        import traceback
        traceback.print_exc()


# 删除流表函数
def flow_delete(in_port):

    # 请求接口，认证，头部，连接信息
    url_in = "/restconf/config/opendaylight-inventory:nodes/node/" + switch_id + "/flow-node-inventory:table/0/flow/" + in_port
    auth = base64.b64encode('admin:admin'.encode())
    headers = {"Authorization": "Basic " + auth, "Content-Type": "application/json"}
    conn = httplib.HTTPConnection('127.0.0.1:8181', timeout=3)
    try:
        conn.request("DELETE", url_in, json.dumps({}), headers)
	    return {'result': 'success'}
    except:
        import traceback
        traceback.print_exc()
	    return {'result': 'failed'}


# meter表删除函数，类似flow_delete函数，不过请求接口的后面有些不同
def meter_delete(meter_id):
    url_in = "/restconf/config/opendaylight-inventory:nodes/node/" + switch_id + "/meter/" + meter_id
    auth = base64.b64encode('admin:admin'.encode())
    headers = {'Authorization': "Basic " + auth, "Content-Type": "application/json"}
    conn = httplib.HTTPConnection('127.0.0.1:8181', timeout=3)
    try:
	    conn.request('DELETE', url_in, json.dumps({}), headers)
	    return {'result': 'success'}
    except:
        import traceback
        traceback.print_exc()
        return {'result': 'failed'}


# 下发流表函数，传入三个参数
def add_flow(flow_info, match, action):
    name = flow_info['name']
    priority = flow_info['priority']
    in_port = match['in_port']

    #请求接口
    url = "/restconf/config/opendaylight-inventory:nodes/node/" + switch_id + "/flow-node-inventory:table/0/flow/" + in_port

    #把接口的属性正确赋值
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
       'order': '1',
        'output-action':{
	    "output-node-connector": switch_id +':' + action['out_port'],
	    "max-length": 65535,
    	}
    }

    #instruction
    instruc_set = {
        "instruction": [{
	    'order': '0',
	    'meter': {'meter-id': action['meter_id']}	
	    },{
            "order": '1',
            "apply-actions": {
                "action": [action_set]
            }
        }]
    }

    #把上面封装好的数据，都添加到flow_set里面
    flow_set['match'] = match_set
    flow_set['instructions'] = instruc_set

    # 根据传递的参数数据，创建一个Table
    table = Table(flow=name, flow_node=switch_id, priority=priority, inport=in_port, match=match_set, instruction=instruc_set)

    # 把flow_set封装好赋值给body
    body = json.dumps({"flow":flow_set})
    #调用pre_put()函数，并获取返回值
    res = pre_put(url, body)
    #如果下发成功，则将创建的table存储到数据库
    if(res['result'] == 'add success'):
	    table.save()
    return res


# 下发meter表，字段相对简单
def add_meter(meter):
    meter_id = meter['meter_id']
    meter_type = meter['meter_type']
    band_rate = meter['band_rate']
    band_size = meter['band_size']
    url = '/restconf/config/opendaylight-inventory:nodes/node/' + switch_id + '/meter/' + meter_id
    
    meter_set = {
        "meter-id": meter_id,
        "meter-band-headers": {
            "meter-band-header": {
                "band-id": '0',
            "meter-band-types": {"flags": meter_type},
            "drop-burst-size": band_size,
            "drop-rate": band_rate
            }
        },
        "meter-name": "guestMeter",
        "container-name": "guestMeterContainer",
        "flags": "meter-kbps"
    }
    meter = Meter(meter=meter_id, meterType=meter_type, bandRate=band_rate, bandSize=band_size)
    body = json.dumps({"meter": meter_set})
    res = pre_put(url, body)
    if(res['result'] == 'add success'):
	    meter.save()
    return res
```

+ models.py(定义主要部分，不重要的可以抽象成一个字段)

```python
class Table(models.Model):
    id = models.AutoField(primary_key=True)
    # flow对应name
    flow = models.CharField(default='', max_length=512)
    #flow_node对应Node-id/switch_id
    flow_node = models.CharField(default='', max_length=256)
    priority = models.CharField(default='', max_length=128)
    inport = models.CharField(default='', max_length=64)
    match = models.CharField(max_length=1024)
    instruction = models.CharField(max_length=1024)


class Meter(models.Model):
    id = models.AutoField(primary_key=True)
    # meter 对应meter_id
    meter = models.CharField(max_length=256)
    meterType = models.CharField(max_length=256)
    bandRate = models.CharField(max_length=256)
    bandSize = models.CharField(max_length=256)
```

## 主要问题

### 前端

+ CSS采用bootstrap框架，直接搭建就可以

+ HTML
    + html页面中元素的name，id等属性的命名问题
    + for循环展示列表是，每行的id不能都是一样的，所以要做一些处理，比如加上一些字符串

+ JavaScript
    + 获取表单信息时要把获取到的数据转化成json格式，方便后端操作
    + 获取到后端返回的result字段之后，判断函数是否执行成功，给出提示之后，要用location.load()进行页面刷新数据
    + ajax请求接口时，数据格式必须跟自己传的数据保持一致，比如json

### 后端

+ urls配置
    + 可以在配置文件夹下面的urls里面include创建的app的urls
    + 然后app的urls都集成在app的目录下面，方便维护和管理

+ views.py
    + views函数接收到前端传来的数据之后要进行json.loads()，解析
    + 然后通过字典访问的方式获取字段值
    + 处理完之后要返回给前端反馈数据

+ models.py
    + models定义类型，要想好那些字段是需要单独用的，哪些是不用的

+ odl.py
    + 删除流表后面的id必须是添加流表时设置的id，不能都是1
    + 添加流表和meter表时，url要拼接对，属性也要设置正确
    