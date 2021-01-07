---
title: java socket demo
date: 2015-05-18 01:15:41
categories: j2ee
tags:
      - java
description: Java socket demo,server & client.
---

socket很早以前就接触过了，前几天有个同事要我写个demo做测试，我很爽快的答应了。代码很快写好，却发现client端接收不到server端返回的信息（报 socket is closed 错误），倒腾了很久才解决；所以在此记下来，防止以后重蹈覆辙。

#### server端代码：
```
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketServer {
	private static final int PORT = 8797;

	public static void main(String[] args) {
		SocketServer server = new SocketServer();
		server.init();
	}

	public void init() {
		ServerSocket serverSocket = null;
		try {
			serverSocket = new ServerSocket(PORT);
			System.out.println("server已开启，在等待client信息。");
			while (true) {
				Thread.sleep(300);
				Socket client = serverSocket.accept();
				 //一个客户端连接就开线程处理读写
				new Thread(new HandlerThread(client)).start();
			}
		} catch (Exception e) {
			e.printStackTrace();
		} 
	}

	/*
	 * 处理读操作的线程
	 */
	private class HandlerThread implements Runnable {
		private Socket client;

		public HandlerThread(Socket client) {
			this.client = client;
		}

		@Override
		public void run() {
			BufferedReader reader = null;
			PrintStream print=null;
			try {
				// 读取客户端数据
				reader = new BufferedReader(new InputStreamReader(client.getInputStream()));;
				char[] c=new char[4096];
				int count = 0;
				if ((count = reader.read(c)) > 0) {
					String str= getInfoBuff(c, count);
					System.out.println("client:" + str);
				}
				
				// 向客户端回复信息
				print = new PrintStream(client.getOutputStream());
				// 发送数据
				print.print("server has received your mssage");
				
			} catch (Exception e) {
				e.printStackTrace();
			} finally {
				try {
					if (reader != null) {
						reader.close();
					}
					if(print!=null){
						print.close();
					}
					if(client!=null){
						client.close();
					}
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
	}
	
	private String getInfoBuff(char[] buff, int count) {
		char[] temp = new char[count];
		for (int i = 0; i < count; i++) {
			temp[i] = buff[i];
		}
		String str = new String(temp);
		//System.out.println(str);// ----------
		return str;
	}
}
```

#### client 端代码：
```
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;

public class SocketClient {

	private String ip = "127.0.0.1";
	private int port = 8797;
	private Socket client=null;
	private PrintStream print = null;
	private BufferedReader reader = null;

	// 执行程序
	public static void main(String[] args) {
		SocketClient socketClient=new SocketClient();
		socketClient.initData();
	}

	private void initData() {
		try {
			if (client == null ) {
				client = new Socket(ip, port);
				client.setSoTimeout(3000);
				if (client.isConnected()) {
					System.out.println("client 已连接，正要发信息给server。");
					print = new PrintStream(client.getOutputStream(), true, "utf-8");
					reader = new BufferedReader(new InputStreamReader(client.getInputStream()));
					
					new Thread(new HandlerThread()).start();
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	/*
	 * 处理读操作的线程
	 */
	private class HandlerThread implements Runnable {
		@Override
		public void run() {
			try {
				// 发送信息给server
				print.print("message from client");
				print.flush();
				/*
				 * 将输出流放在输入流之前关闭，在执行"reader.read(buffer)"代码时会报socket is closed错误。
				 * 原因是输入流关闭了会导致socket关闭，原因不清楚；所以还要使用socket时不要先关闭输出流。
				 * if (print != null) {// 此代码要放在执行完reader.read(buffer)之后。
				 * print.close();
				 * }
				 */
				
				// 接收server的信息
				char[] buffer = new char[4096];
				String str = "";
				int count = 0;
				if ((count = reader.read(buffer)) > 0) {
					str = parseString(buffer, count);
					System.out.println(str);
				}

			} catch (Exception e) {
				e.printStackTrace();
			} finally {
				try {
					if (print != null) {
						print.close();
					}
					if (reader != null) {
						reader.close();
					}
					if (!client.isClosed()) {
						client.close();
					}
				} catch (Exception e) {
					e.printStackTrace();
				}
			}

		}// end run

	}// end MessageThread

	private String parseString(char[] buff, int count) {
		char[] temp = new char[count];
		for (int i = 0; i < count; i++) {
			temp[i] = buff[i];
		}
		String str = new String(temp);
		return str;
	}
}
```
