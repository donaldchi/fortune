python cgi programming: fortune

program on the local side
```javascript:options.js
// Saves options to chrome.storage
function save_options() {
  var user_name = document.getElementById('user_name').value;
  var sex_m = document.getElementById('sex_m').checked;
  var sex_f = document.getElementById('sex_f').checked;
  var birth_yyyy = document.getElementById('birth_yyyy').value;
  var birth_mm = document.getElementById('birth_mm').value;
  var birth_dd = document.getElementById('birth_dd').value;
  var birth_hh = document.getElementById('birth_hh').value;
  var birth_solunar = document.getElementById('birth_solunar').value;
  var target_yyyy = document.getElementById('target_yyyy').value;

  chrome.storage.sync.set({
    uname : user_name,
    usex_m : sex_m,
    usex_f : sex_f,
    birth_y : birth_yyyy,
    birth_m : birth_mm,
    birth_d : birth_dd,
    birth_h : birth_hh,
    birth_s : birth_solunar,
    target_y : target_yyyy
  }, function() {
    // Update status to let user know options were saved.
    document.getElementById('results').src='http://servername/cgi-bin/submit_info.py?uname='+user_name
                          + '&usex_m='  + sex_m  + '&usex_f='  + sex_f
                          + '&birth_y=' + birth_yyyy + '&birth_m='  + birth_mm
                          + '&birth_d=' + birth_dd + '&birth_h='  + birth_hh
                          + '&birth_s=' + birth_solunar + '&target_y=' + target_yyyy;

    document.getElementById("results").contentWindow.location.reload();
  });
}

// Restores select box and checkbox state using the preferences
// stored in chrome.storage.
function restore_options() {
  // set default value
  chrome.storage.sync.get({
    uname : '',
    usex_m : true,
    usex_f : false,
    birth_y : '1985',
    birth_m : '02',
    birth_d : '14',
    birth_h : '0',
    birth_s : 'S_C',
    target_y : '2016'
  }, function(items) {
    document.getElementById('user_name').value  = items.uname;
    document.getElementById('sex_m').checked = items.usex_m;
    document.getElementById('sex_f').checked = items.usex_f;
    document.getElementById('birth_yyyy').value = items.birth_y;
    document.getElementById('birth_mm').value = items.birth_m;
    document.getElementById('birth_dd').value = items.birth_d;
    document.getElementById('birth_hh').value = items.birth_h;
    document.getElementById('birth_solunar').value = items.birth_s;
    document.getElementById('target_yyyy').value = items.target_y;

    document.getElementById('results').src='http://54.201.252.209/cgi-bin/submit_info.py?uname='+items.uname
                          + '&usex_m='  + items.usex_m  + '&usex_f='  + items.usex_f
                          + '&birth_y=' + items.birth_y + '&birth_m='  + items.birth_m
                          + '&birth_d=' + items.birth_d + '&birth_h='  + items.birth_h
                          + '&birth_s=' + items.birth_s + '&target_y=' + items.target_y;
  });
}

document.addEventListener('DOMContentLoaded', restore_options);
document.getElementById('save').addEventListener('click',
    save_options);
```

program on the server side
```python:submit_info.py
#!/usr/bin/env python
#_*_coding: utf-8 _*_

import requests
import cgi, cgitb

form = cgi.FieldStorage()
uname = form.getvalue('uname');
usex_m = form.getvalue('usex_m');
usex_f = form.getvalue('usex_f');
birth_y = form.getvalue('birth_y');
birth_m = form.getvalue('birth_m');
birth_d = form.getvalue('birth_d');
birth_h = form.getvalue('birth_h');
birth_s = form.getvalue('birth_s');
target_y = form.getvalue('target_y');

url = "http://www.unsin.co.kr/unse/free/today/result"

if usex_m=='true': sex = '남'
else: sex = '여'
payload = {'user_name': uname,
'sex': sex,
'birth_yyyy': birth_y,
'birth_mm': birth_m,
'birth_dd': birth_d,
'birth_hh': birth_h,
'birth_solunar': birth_s,
'target_yyyy': target_y}

results = requests.post(url, payload)
contents = results.text

contents = contents[contents.find("<div class=\"cont\">"):contents.find("<!--// Contents -->")]
delStr = contents[contents.find("<div class=\"tab\">"):contents.find("<!--datetab-->")]
contents = contents.replace(delStr, "")
contents = contents.replace("src=\"", "src=\"http://www.unsin.co.kr")
contents = contents.replace("\"score_bg\"><em>", "\"score_bg\"><em>오늘의 일진점수: ");
# css= ["<link rel=\"stylesheet\" type=\"text/css\" href=\"/css/common.css\">\n",
#  	  "<link rel=\"stylesheet\" type=\"text/css\" href=\"/css/sub.css\">\n",
#  	  "<link rel=\"stylesheet\" type=\"text/css\" href=\"/css/free.css\">\n"]
# contents = "".join(css) + contents
# print(contents+uname+","+usex_m+","+usex_f+","+birth_y+","+birth_m+","+birth_d+","+birth_h+","+birth_s+","+target_y+ "sex="+sex)
print(contents)
```
