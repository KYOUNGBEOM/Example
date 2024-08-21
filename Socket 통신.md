# Client
```java
package client;

import java.awt.*;
import java.awt.event.*;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;
import java.net.UnknownHostException;
public class Main {
	Frame f;
	TextArea ta;
	Panel p;
	TextField tf;
	Button b;
	String id;
	Socket s;
	DataOutputStream dos;
	DataInputStream dis;
	
	public Main() {
		f=new Frame("chat");
		ta=new TextArea();
		p=new Panel();
		tf=new TextField("채팅 ID를 입력하세요",20);
		b=new Button("connect");
		
		f.add(ta);
		f.add(p, BorderLayout.SOUTH);
		p.add(tf);
		p.add(b);	
		
		tf.addActionListener(e->{
			try { 
				String msg=id+tf.getText();
				dos.writeUTF(msg);
				dos.flush();
				tf.setText("");
			}catch(Exception ex) {
				ex.printStackTrace();
			}
		});
		
		
		b.addActionListener(e->{
			try {
				s=new Socket("127.0.0.1",3000);
				ta.append("연결 완료\n");
				b.setEnabled(false);
				id="["+tf.getText()+"]"; //[은수] [길동]
				dos=new DataOutputStream(s.getOutputStream());
				dis=new DataInputStream(s.getInputStream());
				
				ClientThread t=new ClientThread();
				t.start();
				
				dos.writeUTF(id+"님이 입장하셨습니다");
				dos.flush();
			} catch (UnknownHostException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			} catch (IOException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
		});
		
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});
		
		f.setSize(400,500);
		f.setVisible(true);
	}
	
	class ClientThread extends Thread{
		@Override
		public void run() {
			while(true) {
				try {
					//읽기
					String msg=dis.readUTF();
					//확인
					ta.append(msg+"\n");
					
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			
		}
	}
	
	public static void main(String[] args) {
		new Main();
	}
}
```

# Server
```java
package server;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.*;
import java.util.*;

public class Server {
	List<DataOutputStream> list=new ArrayList<>();
	
	public Server() {
		try {
			ServerSocket ss=new ServerSocket(3000);
			while(true) {
				Socket s=ss.accept();
				System.out.println("connect ok");
				
				ServerThread t=new ServerThread(s);
				t.start();
			}
			
			
			
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	class ServerThread extends Thread{
		Socket s;
		DataInputStream dis;
		DataOutputStream dos;
		
		public ServerThread(Socket s) {			
			try {
				this.s=s;
				dis=new DataInputStream(s.getInputStream());
				dos=new DataOutputStream(s.getOutputStream());		
				list.add(dos);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			
		}
		
		private void broadcast(String msg) {
			synchronized (list) {
				for(DataOutputStream dos:list) {
					try {
						dos.writeUTF(msg);
					} catch (IOException e) {
						list.remove(dos);
						
					}
				}
			}			
		}

		@Override
		public void run() {			
			try {
				while(true) {
					//읽는 일
					String msg=dis.readUTF();
					//broadcast
					broadcast(msg) ;
				}
			} catch (IOException e) {
				System.out.println(s.getInetAddress()+ " 접속 종료");
				try {
					dis.close();
					dos.close();
					s.close();
				}catch(Exception ex) {}				
			}
		}
	}

	public static void main(String[] args) {
		new Server();
	}
}
```
