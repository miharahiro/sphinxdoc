==============================
Pengine - crmd
==============================

:Date: 2016-06-06
:Authosrs: 大和さん

* crmdでは pegineは pb_subsystem->sourceで表現されている。通信時にこれを指定する。

* crmdは do_pe_invoke_callback経由でpengineに処理を仰ぐ。

* pegineは pe_ipc_dispatchでcrmdからの依頼を受ける。

* pegineはcrmdからの依頼があると、次に何をすれば良いかをdo_calculationsにてプログラミングしてあげる。

* プログラミングの結果はxmlNodeにまとめて、crmdに送る。このといprocess_pe_message関数を使って送信する。
  
* crmdは pe_ipc_dispatchでpegineで計算した結果を受けとる。

* リソースの配置(？)は、その種類毎にクラスがあり、異なる方法で処理される。resource_class_alloc_functions
