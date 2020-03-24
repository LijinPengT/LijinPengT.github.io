---
title: 隐匿传输代码优化
date:  2020-03-24 20:55:55 +0800
category: blockchain
tags: hiddenTransmission
excerpt: 优化隐匿传输模块代码与开销测试模块代码
---

# 隐匿传输模块的代码优化
> 只是部分代码, 不能运行

## 隐匿传输固定开销模块
> 固定开销模块还需要函数式编程优化, 等最终方案确定 

### 代码结构优化
1. 代码布局优化
3. 使用let声明变量, 防止全局污染
4. consolo关键信息, 便于理解每一块的数据都是哪一部分的

+ Old-Code(不光测试的时间不对, 而且测试promise函数执行的时间的方式也有问题)
    ```JavaScript
    ...
    var startTime,endTime; 
    var startDate=new Date();  
    ...
    console.log("--------------------------");
    startTime = new Date();
    eccrypto.derive(r, publicKeyA).then(function(rA) {
        startTime = new Date();
        console.log("rA:", rA);
        var ST= ecparams.G.multiply(BigInteger.fromBuffer(rA));//ST=H(rA)*G
        var x1 = ST.affineX.toBuffer(32);
        var y1 = ST.affineY.toBuffer(32);
        // ST2==>数字签名
        var ST2 = Buffer.concat([new Buffer([0x04]), x1, y1]);
        console.log("ST:", ST2);
        //const sha256 = crypto.createHash("sha256");
        //sha256.update(ST.hexSlice());
        // console.log("hash256:"+sha256.digest("hex"));
        var pubB = ecparams.G.multiply(BigInteger.fromBuffer(privateKey_b));
        var PKSA = pubB.add(ST);
        // console.log("PKSA=:",PKSA);
        var x2 = PKSA.affineX.toBuffer(32);
        var y2 = PKSA.affineY.toBuffer(32);
        var PKSA2 = Buffer.concat([new Buffer([0x04]), x2, y2]);
            console.log("PKSA:",PKSA2);
        endTime = new Date();
        console.log("加密秘钥时间："　+ (endTime - startTime) + "ms");
        // Encrypting the message for B.
        console.log("--------------------------");
        startTime = new Date();
        eccrypto.encrypt(PKSA2, Buffer.from("msg to b")).then(function(encrypted) {
            // B decrypting the message.
            var c1=encrypted;
            console.log("encrypted=:",c1);
            console.log("--------------------------");
            startTime = new Date();
            eccrypto.derive(privateKey_a, R).then(function(aR) {
                console.log("aR:", aR);
                startTime = new Date();
                var BN = web3.utils.BN;
                //var a=new BN(privKey).toString();
                var SKSA_bn=new BN(aR).add(new BN(privateKey_b)).toString();//SKSA=H(aR)+b
                var SKSA=web3.utils.toHex(SKSA_bn);
                var SKSA_string=SKSA.toString().replace("0x","");
                console.log("SKSA_bn=:",SKSA_bn);
                console.log("SKSA_string=:",SKSA_string);
                console.log( "privateKey_a:",privateKey_a);
                endTime = new Date();
                console.log("解密秘钥时间：" + (endTime - startTime) + "ms");
                var SKSA_buf=new Buffer(SKSA_string,"hex");
                console.log("SKSA_buf=:",SKSA_buf);
                SKSAG=ecparams.G.multiply(BigInteger.fromBuffer(SKSA_buf));
                var x3 = SKSAG.affineX.toBuffer(32);
                var y3 = SKSAG.affineY.toBuffer(32);
                var SKSAG2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
                console.log( "SKSAG:",SKSAG2);
                console.log("--------------------------");
                startTime = new Date();
                eccrypto.decrypt(SKSA_buf, encrypted).then(function(plaintext) {
                    console.log("Message to part B:", plaintext.toString());
                }).catch(function(err) {
                    console.log(err);
                });
                endTime = new Date();
                console.log("解密信息时间： " + (endTime - startTime) + "ms");
            });
            endTime = new Date();
            console.log("公钥计算时间： " + (endTime - startTime) + "ms");
        });
        endTime = new Date();
        console.log("加密信息时间： " + (endTime - startTime) + "ms");
    });
    var endDate=new Date(); 
    console.log("总的消耗时间" + (endDate-startDate) +'ms');
    ```

+ New-Code(打印必要信息, 结构清晰, 时间测试方法正确)
    ```JavaScript
        console.log("--------------------------");
        console.log("发送方计算加密秘钥rA");
        let startEnPubKA = process.hrtime();
        eccrypto.derive(r, publicKeyA).then(function(rA) {
            let endEnPubKA = process.hrtime(startEnPubKA);
            console.log("rA:", rA);
            console.log("发送方计算加密秘钥rA的时间: ", endEnPubKA[1]*1e-6, "ms");
            console.log("--------------------------");
            console.log("发送方计算隐匿标签ST...");
            let startCalST = process.hrtime();
            var ST= ecparams.G.multiply(BigInteger.fromBuffer(rA));//ST=H(rA)*G
            var x1 = ST.affineX.toBuffer(32);
            var y1 = ST.affineY.toBuffer(32);
            // ST2==>数字签名
            var ST2 = Buffer.concat([new Buffer([0x04]), x1, y1]);
            let endCalST = process.hrtime(startCalST);
            console.log("ST:", ST2);
            console.log("发送方计算隐匿标签ST的时间: ", endCalST[1]*1e-6, "ms");
            console.log("--------------------------");
            console.log("发送方消息加密秘钥计算PKSA...");
            let startCalPKSA = process.hrtime();
            var pubB = ecparams.G.multiply(BigInteger.fromBuffer(privateKey_b));
            var PKSA = pubB.add(ST);
            // console.log("PKSA=:",PKSA);
            var x2 = PKSA.affineX.toBuffer(32);
            var y2 = PKSA.affineY.toBuffer(32);
            var PKSA2 = Buffer.concat([new Buffer([0x04]), x2, y2]);
            let endCalPKSA = process.hrtime(startCalPKSA);
            console.log("PKSA:",PKSA2);
            console.log("发送方计算消息加密秘钥PKSA的时间: ", endCalPKSA[1]*1e-6, "ms");
            console.log("--------------------------");
            console.log("发送方加密发送信息...");
            let startEnMsg = process.hrtime();
            eccrypto.encrypt(PKSA2, Buffer.from("msg to b")).then(function(encrypted) {
                let endEnMsg = process.hrtime(startEnMsg);
                var c1=encrypted;
                console.log("c1: ",c1);
                console.log("bytes of c1 ", Buffer.from("msg to b").buffer.byteLength, "bytes");
                console.log("发送方对称算法加密信息的时间: ", endEnMsg[1]*1e-6, "ms");
                console.log("--------------------------");
                console.log("接收方提取交易公钥...");
                let startFetchR = process.hrtime();
                eccrypto.derive(privateKey_a, R).then(function(aR) {
                    let endFetchR = process.hrtime(startFetchR);
                    console.log("aR:", aR);
                    console.log("接收方提取交易公钥R的时间: ", endFetchR[1]*1e-6, "ms");
                    console.log("--------------------------");
                    console.log("使用私钥对计算ST'......");
                    console.log("--------------------------");
                    console.log("接收方计算解密秘钥SKSA...");
                    let startCalSKSA = process.hrtime();
                    var BN = web3.utils.BN;
                    var SKSA_bn=new BN(aR).add(new BN(privateKey_b)).toString();//SKSA=H(aR)+b
                    var SKSA=web3.utils.toHex(SKSA_bn);
                    var SKSA_string=SKSA.toString().replace("0x","");
                    console.log("SKSA_bn=:",SKSA_bn);
                    console.log("SKSA_string=:",SKSA_string);
                    console.log( "privateKey_a:",privateKey_a);
                    
                    var SKSA_buf=new Buffer(SKSA_string,"hex");
                    console.log("SKSA_buf=:",SKSA_buf);
                
                    let SKSAG=ecparams.G.multiply(BigInteger.fromBuffer(SKSA_buf));
                    var x3 = SKSAG.affineX.toBuffer(32);
                    var y3 = SKSAG.affineY.toBuffer(32);

                    var SKSAG2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
                    let endCalSKSA = process.hrtime(startCalSKSA);
                    console.log( "SKSAG:",SKSAG2);
                    console.log("接收方计算解密秘钥的时间: ", endCalSKSA[1]*1e-6, "ms");
                    
                    console.log("--------------------------");
                    console.log("接收方使用SKSA解密信息...");
                    let startDecMsg = process.hrtime();
                    eccrypto.decrypt(SKSA_buf, encrypted).then(function(plaintext) {
                        let endDecMsg = process.hrtime(startDecMsg);
                        console.log("Message to part B:", plaintext.toString());
                        console.log("接收方解密信息时间: ", endDecMsg[1]*1e-6, "ms");

                        endDate = process.hrtime(startDate);
                        console.log("除了计算ST'之外的固定开销总时间为: ", endDate[1]*1e-6, "ms");
                    }).catch(function(err) {
                        console.log(err);
                    });
                });
            });
        });
    ```

## 隐匿传输接收方匹配隐匿标签模块

### 代码结构的优化
1. 代码布局优化
2. 函数式编程, 把很多的for循环, 拆解成模块化的函数
3. 使用let声明变量, 防止全局污染
4. consolo关键信息, 便于理解每一块的数据都是哪一部分的

+ Old-Code(like a sea of shit)
    ```JavaScript
        // ST'计算与匹配
		console.log("ST'计算与匹配模拟");
		var firstTimes = [];
		var secondTimes = [];
		var thirdTimes = [];
		var totalTime;
		for(var i = 0; i < 3; i++) {
			if(i === 0){
				for(var j = 0; j < 100; j++) {
					startTime = new Date();
					for(var k = 0, rand = getRndInteger(10, 20); k < rand; k++) {
						eccrypto.derive(privateKey_a, R).then(function(aR){
							//console.log("aR:", aR);
							var _ST= ecparams.G.multiply(BigInteger.fromBuffer(aR));
							var x3 = ST.affineX.toBuffer(32);
							var y3 = ST.affineY.toBuffer(32);
							var _ST2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
						});
					}
					endTime = new Date();
					totalTime = (endTime - startTime);
					firstTimes[j] = totalTime;
				}
			} else if(i === 1) {
				for(var n = 0; n < 100; n++) {
					startTime = new Date();
					for(var k1 = 0, rand1 = getRndInteger(10, 20); k1 < rand1; k1++) {
						eccrypto.derive(privateKey_a, R).then(function(aR){
							//console.log("aR:", aR);
							var _ST= ecparams.G.multiply(BigInteger.fromBuffer(aR));
							var x3 = ST.affineX.toBuffer(32);
							var y3 = ST.affineY.toBuffer(32);
							var _ST2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
						});
					}
					endTime = new Date();
					totalTime = (endTime - startTime);
					secondTimes[n] = totalTime;
				}
			} else {
				for(var m = 0; m < 100; m++) {
					startTime = new Date();
					for(var k2 = 0, rand2 = getRndInteger(10, 20); k2 < rand2; k2++) {
						eccrypto.derive(privateKey_a, R).then(function(aR){
							//console.log("aR:", aR);
							var _ST= ecparams.G.multiply(BigInteger.fromBuffer(aR));
							var x3 = ST.affineX.toBuffer(32);
							var y3 = ST.affineY.toBuffer(32);
							var _ST2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
						});
					}
					endTime = new Date();
					totalTime = (endTime - startTime);
					thirdTimes[m] = totalTime;
				}
			}
			
		}
		console.log("firstTimes: " + firstTimes);
		console.log("secondTimes: " + secondTimes);
		console.log("thirdTimes: " + thirdTimes);
    ```

+ New-Code(舒服太多了, 也解决了for循环调用promise().then()的问题)
    ```JavaScript
        // ST'计算与匹配
		console.log("ST'计算与匹配模拟");
		var times = [];
		var promiseArray = [];

		// 使用随机值模拟匹配ST
		function calST() {
			for(let k = 0, rand = getRndInteger(10, 20); k < rand; k++) {
				promiseArray.push(eccrypto.derive(privateKey_a, R));
			}
			Promise.all(promiseArray).then(function(aR){
				var _ST= ecparams.G.multiply(BigInteger.fromBuffer(aR));
				var x3 = ST.affineX.toBuffer(32);
				var y3 = ST.affineY.toBuffer(32);
				var _ST2 = Buffer.concat([new Buffer([0x04]), x3, y3]);
			});
		}

		// 重复测试100次匹配ST
		function repeatCalST(len, times) {
			for(let i = 0; i < len; i++){
				let start = process.hrtime();
				calST();
				let end = process.hrtime(start);
				times.push((end[1]*1e-6).toPrecision(6));
			}
		}
		for(let i = 0; i < 3; i++) {
			times[i] = new Array();
			repeatCalST(100, times[i]);
		}
		console.log("**************");
		console.log("[firstTimes]: " + times[0]);

		console.log("**************");
		console.log("[secondTimes]: " + times[1]);

		console.log("**************");
		console.log("[thirdTimes]: " + times[2]);
    ```

## 时间测试代码的优化
1. 使用let声明时间变量, 防止全局污染
2. 给每个时间变量语义化, 方便维护和阅读
3. 使用process.hrtime(), 虽然性能没有Date.now()/Date().getTime()/process.uptime()好, 但是精确到纳秒, 符合实验测试要求

+ Old-Code
    ```JavaScript
    var startTime = new Date();
    func();
    var endTIme = new Date();
    var totalTime = (endTime - startTime);
    ```

+ New-Code
    ```JavaScript
    let start = process.hrtime();
    func();
    let end = process.hrtime(start);
    let total = end[1]*1e-6;
    ```