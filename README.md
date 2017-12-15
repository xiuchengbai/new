# new/*
 * To change this template, choose Tools | Templates
 * and open the template in the editor.
 */

 package lab1;


import java.io.*;
import java.net.*;

import java.util.Scanner;

public class Client

{
    @SuppressWarnings("empty-statement")
        static String clientpath = "E:\\client\\";
	static String path = clientpath;

    public static void main(String[] args)
    {
        Socket sk;
        String w;
        DataInputStream is ;
        DataOutputStream os;
        BufferedInputStream reader;
        FileOutputStream files=null;
        BufferedWriter writer;
         byte[] buf=new byte[1024];
     try
        {
          sk = new Socket("localhost",3333);
          os = new DataOutputStream(sk.getOutputStream());
          is = new DataInputStream(sk.getInputStream());
          Scanner sc = new Scanner ( System.in );
         reader=new BufferedInputStream(sk.getInputStream());
         writer=new BufferedWriter(new OutputStreamWriter(sk.getOutputStream()));

          while(true)
          {
             
             
              System.out.println("input'displayDirectory':  will display server directory");
              System.out.println("input'!displayDirectory': will display client directory");
              System.out.println("input'list' :             you can get the fileList from Server");
              System.out.println("input'get' :              you can get the fileList from Server");
              System.out.println("input 'upload',           you can upload the file.");        
              System.out.println("input 'exit',             you can exit the file.");
              w = sc.nextLine();
             os.writeUTF(w+"\r\n");          //writeUTF(string str)
             os.flush();
             

             //reader.read(buf);

              if(w.equals("get"))      
              {
                   System.out.println("get file from server.");
                   String file = is.readUTF();
                   System.out.println(file);
                   System.out.println("please input the file name you want to download锛?);
                   String name  = sc.nextLine();
                   System.out.println("filename="+name);
                   os.writeUTF(name);
                  
                   File f=new File("E:/client/"+name);
                   if(f.exists()){
                       f.delete();
                   } 
                  
                   files=new FileOutputStream(f);
                   byte[] stream=new byte[1024];
		  	    int count=0;
		  	    long fileLength=0;
		  	    int first=0;
		  	    long index=0;

                           
		  	    while((count=reader.read(stream)) != -1){

                                //System.out.println("200");
		  	    	first++;
		  	    	if(first == 1){
		  	    		String transferContent=new String(stream).trim();
		  	    		String[] sizeStr=transferContent.split(":");
		  	    		System.out.println("Filelength=>"+sizeStr[0]);
		  	    		fileLength=Long.parseLong(sizeStr[0]);
		  	    		index+=count-(fileLength+":").getBytes().length;
                                       

		  	    		files.write(stream,(sizeStr[0]+":").getBytes().length,(count-(sizeStr[0]+":").getBytes().length));
		  	    	}else{
		                index+=count;
		                files.write(stream,0,count);
		  	    	}
	                files.flush();
	                stream=new byte[1024];

		  	    	if(index == fileLength){
		  	    		System.out.println("file transfer success!");
                     	    		break;
		  	    	}
		  	    }
                    System.out.println(index);
		  	    if(sk!=null){
				  sk.close();
				}
				if(reader!=null){
					reader.close();
				}
				if(writer!=null){
					writer.close();
				}
                 
         
               }
              if(w.equals("list"))
              {
                   System.out.println("get file from server.");
                   String file = is.readUTF();
                   System.out.println(file);                  
               }
               if(w.equals("upload"))   
               {
                 //list the files in current directory
                 System.out.println("Please choose one file in below list:");
                 File fileList = new File("E:/client/");
                 String directory = "";
                 String[] list = fileList.list();
                 for(int i=0;i<list.length;i++)
                 {
                    System.out.println(list[i]);
                    directory=directory+list[i]+"\n";
                 }
                 System.out.println("please enter the file name");
                 String filename = sc.nextLine();
                 //Step 1: send the filename
                 os.writeUTF(filename);//      os.flush();
                 //Step 2: send the content of the file
                 System.out.println(position(filename));
                 FileInputStream fis = new FileInputStream( position(filename) );
                 int data,sum=0;
                        while ( -1 != ( data = fis.read()) )
                        {
                            os.write( data );
                            os.flush();
                            sum+=1;
                        }
                 fis.close();
                 System.out.println("File "+filename+" is uploaded! ("+sum+" bytes)");
               }
            
              
               if (w.equals("!displayDirectory")) {
    		System.out.println("Current local path: " + path);
                }
               if (w.equals("displayDirectory")) {
                   String file = is.readUTF();
                   System.out.println(file);
               }
               if(w.equals("exit"))
                {
                 is.close();
                 os.close();
                 sk.close();
                 System.out.println("CLIENT exit the system");
                 break;
                 }
             }
        }
        catch (IOException e)
        {
            System.out.println(e.toString());
        }
   }



    private static File position( String f )
    {
      String postion="E:/client/";
      File file = new File(postion+f);
      return file;
    }



}
