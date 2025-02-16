<?php

//函数区

function isPortOpenTCP($ip, $port, $showMessage = false, $searchSpeed = 1) {
   //isPortOpen($ip, $port, true);  打印每条端口查询信息
   //ipPortOpen($ip, $port, false);  不打印端口查询信息
    $connection = @fsockopen($ip, $port, $errno, $errstr, $searchSpeed);
    if ($connection){
        fclose($connection);
        if ($showMessage) {
            echo "扫描到 $ip 的 $port 端口打开<br>";
        }
        return 1;
    } else {
        if ($showMessage) {
            echo "扫描到 $ip 的 $port 端口关闭&nbsp&nbsp&nbsp连接错误: $errno - $errstr<br>";
        }
        return 0;
    }
}//端口数据返回判断，有数据返回则端口开启，无则是关闭


function isPortOpenUDP($ip, $port, $showMessage = false, $searchSpeed = 1) {
   //isPortOpen($ip, $port, true);  打印每条端口查询信息
   //ipPortOpen($ip, $port, false);  不打印端口查询信息
    $connection = @stream_socket_client("udp://$ip:$port", $errno, $errstr, $searchSpeed, STREAM_CLIENT_CONNECT);
    if ($connection){
        fclose($connection);
        if ($showMessage) {
            echo "扫描到 $ip 的 $port 端口打开<br>";
        }
        return 1;
    } else {
        if ($showMessage) {
            echo "扫描到 $ip 的 $port 端口关闭<br>连接错误: $errno - $errstr<br>";
        }
        return 0;
    }
}//端口数据返回判断，有数据返回则端口开启，无则是关闭

function getOnePort($searchIpA, $portA, $udpORtcp = "UDP"){
   echo "开始扫描端口 $portA<br>";
   $isPortOpen = ($udpORtcp=="UDP"||$udpORtcp == "udp")?isPortOpenUDP($searchIpA, $portA, true):isPortOpenTCP($searchIpA, $portA, true);//此处打印结果
   if ($udpORtcp == "UDP" || $udpORtcp == "udp"){
      //UDP执行区

   } else if($udpORtcp == "TCP" || $udpORtcp == "tcp") {
      //TCP执行区

   } else {
      //0执行区
      echo "未选择UDP或TCP参数";
      exit(-1);
   }
}//获取指定ip地址的 一个 端口开关情况

function getSomePorts($searchIpB, $portSarB, $portEndB, $udpORtcp = "UDP", $searchSpeed=0.1, $sleepTime = 0){
   //端口扫描，遍历所有端口信息
   echo "开始遍历 $searchIpB 的 $portSarB 到 $portEndB 端口<br>";
   if($portSarB>$portEndB){
      echo "开始端口应小于结束端口！<br>";
      exit(-1);
   }
   $portINF = [];
   if ($udpORtcp == "UDP" || $udpORtcp == "udp"){
      //UDP执行区
      echo "UDP方法扫描<br>";
      for ($curPort = $portSarB; $curPort <= $portEndB; $curPort++) {
         sleep($sleepTime);//扫描间隔时间，单位s //防止被目标主机ban掉，请适当调高该参数
         $isPortOpenUDP = isPortOpenUDP($searchIpB, $curPort, false, $searchSpeed);//此处可以打印单步结果
         $portINF[$curPort-$portSarB][0] = $curPort;
         $portINF[$curPort-$portSarB][1] = $isPortOpenUDP;
      }
   } else if($udpORtcp == "TCP" || $udpORtcp == "tcp") {
      //TCP执行区
      echo "TCP方法扫描<br>";
      for ($curPort = $portSarB; $curPort <= $portEndB; $curPort++) {
         sleep($sleepTime);//扫描间隔时间，单位s //防止被目标主机ban掉，请适当调高该参数
         $isPortOpenTCP = isPortOpenTCP($searchIpB, $curPort, false, $searchSpeed);//此处可以打印单步结果
         $portINF[$curPort-$portSarB][0] = $curPort;
         $portINF[$curPort-$portSarB][1] = $isPortOpenTCP;
      }
   } else {
      //0执行区
      echo "未选择UDP或TCP参数";
      exit(-1);
   }
   outputPortsINFwithSimple($portINF);//此处可简单打印总和结果
}//获取指定ip地址的 多个 端口开关情况，并两列输出，结果存储在函数内部大小为2×n的portINF数组中

function outputPortsINFwithSimple($portINF){
   //输出格式：简单输出表格，直接在html格式显示，简陋减少服务器负担
   for ($i = 0; $i < count($portINF); $i++) {
      for ($j = 0; $j < count($portINF[$i]); $j++) {
         echo $portINF[$i][$j] . "&nbsp;&nbsp;&nbsp;&nbsp;";
      }
      echo "<br>";
   }
}//简单输出portINF数组，适用于低配环境，减小服务器压力


//执行区

getOnePort("219.231.0.101", 443, "tcp");
getSomePorts("219.231.0.101", 1, 100, "tcp");

//getOnePort("ip地址", 端口号, "套接字方法", 端口扫描速度<默认0.1s>)
//getSomePort("ip地址", 起始端口号, 结束端口号, "套接字方法", 端口扫描速度<默认0.1s>, 扫描睡眠间隔<默认0s>)   扫描睡眠间隔--用于防止主机屏蔽--调高安全--降低速度
?>