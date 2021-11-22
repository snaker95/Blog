```js
(function () {
    var data = {};

    switch (typeof(request.data)) {
        case "object":
        data = request.data;break;
        case "string":
        data = JSON.parse(request.data);break;
    }

    
    var queryParam = pm.request.url.query.members
    for (let i in queryParam) {
        if (queryParam[i]["disabled"] == true) {
            continue
        }
        data[queryParam[i]["key"]] = queryParam[i]["value"]
    }
    var signKey = pm.environment.get('signKey');
    var signSecret = pm.environment.get('signSecret');
    var signKey = "test_key";
    var signSecret = "test_secret";

    signMap = {
        "api_key":signKey,
        "expires": Math.floor((new Date()).getTime()/1000+13600),
        "nonce": randomString(16)
    }
    for (k in signMap) {
        data[k] = signMap[k]
    }
    signMap["sign"]=calcSign(data, signSecret)
    console.log(signMap)
    pm.globals.set('Sign-Auth', JSON.stringify(signMap));
})();

function calcSign(data, signSecret) {
	delete data['sign'];

	var keys = [];
	for(var k in data) {
	    keys.push(k);
	}
    keys.sort();
    
    var kv = [];
    for (var v of keys) {
        kv.push(v + '=' + encodeURI(data[v]));
    }
    var kvStr = kv.join('&');
    console.log(kvStr)

    let sign = CryptoJS.MD5(CryptoJS.enc.Base64.stringify(CryptoJS.HmacSHA256(kvStr, signSecret))).toString();
    
    return sign.substring(5, 15);
}

function randomString(len) {
 　　len = len || 32;
  　　var $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';    /****默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1****/
 　　var maxPos = $chars.length;
 　　var pwd = '';
 　　for (i = 0; i < len; i++) {
 　　　　pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
 　　}
 　　return pwd;
 }
```

