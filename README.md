Practical no:-3  
Implement the Servlet IO and File applications. 
Q.3 a) Create a Servlet application to upload and download a file. indexd.jsp:- <html> 
    <head> 
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">         <title>Download File</title> 
    </head> 
    <body> 
        <h1> File Download App</h1> 
        Click <a href="DownloadServlet?filename=SampleChapter.pdf">Sample Chapter</a>         <br/><br/> 
        Click <a href="DownloadServlet?filename=TOC.pdf">Table Of Contents</a>     </body> 
</html> 
DownloadServlet.java:- import java.io.*; import javax.servlet.*; import javax.servlet.http.*; 
public class DownloadServlet extends HttpServlet 
{ 
    public void doGet(HttpServletRequest req,HttpServletResponse res)throws 
IOException,ServletException 
    { 
        res.setContentType("APPLICATION/OCTET-STREAM"); 
        String filename = req.getParameter("filename"); 
        ServletContext context = getServletContext(); 
        InputStream is= context.getResourceAsStream("/"+filename);         ServletOutputStream os= res.getOutputStream(); 
        res.setHeader("Content-Disposition","attachment;filename=\""+filename+"\"");         int i; 
        byte b[]=new byte[1024];         while ((i=is.read(b))!=-1) 
        {             os.write(b); 
        }         is.close();         os.close(); 
    } } 
Output: 
  indexu.jsp:- 
<%@page contentType="text/html" pageEncoding="UTF-8"%> 
<!DOCTYPE html> 
<html> 
    <head> 
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
        <title>File Upload</title> 
    </head> 
    <body> 
        <form action="UploadServlet" method="post" enctype="multipart/form-data"> 
            Select File To Upload:- <input type="file" name="f1" > 
            Destination :- <input type="text" value="/tmp" name="destination"> 
            <br> 
            <input type="submit" value="Upload File" name="Upload"> 
        </form> 
    </body> 
</html> 
UploadServlet.java:- import java.io.*; import javax.servlet.*; 
import javax.servlet.annotation.MultipartConfig; import javax.servlet.http.*; @MultipartConfig 
public class UploadServlet extends HttpServlet  
{ 
    public void doPost(HttpServletRequest req,HttpServletResponse res)throws 
IOException,ServletException 
    { 
        res.setContentType("text/html");         PrintWriter out = res.getWriter(); 
        String path=req.getParameter("destination"); 
        Part filePart = req.getPart("f1"); 
        String sfilePart = req.getPart("f1").toString();         out.print("<br>FilePart:"+sfilePart); 
        String filename = filePart.getName().toString();         out.print("<br><br><hr> File Name:- "+filename); 
        OutputStream os=null;         InputStream is=null;       try 
        { 
            os=new FileOutputStream(new File(path+File.separator+filename));             is=filePart.getInputStream();             int read=0; 
            byte[] b=new byte[1024]; 
            while((read=is.read(b))!=-1) 
            {                 os.write(b,0,read); 
            } 
            out.println("<br>file uploaded successfully...!!!"); 
        } 
       catch(FileNotFoundException e) 
        { 
            out.print(e);  
        } 
    } 
} 
Output: 








  
Q.3 b) Develop Simple Servlet Question Answer Application using Database.   
MySql Command from mysql software:- 
1.	create database queansdb; 
2.	create table queans(qno int primary key, que varchar(200), op1 varchar(100), op2 varchar(100), op3 varchar(100), op4 varchar(100), ans varchar(3)); 
3.	insert few records. 
4.	add mysql-connector to library folder of the current application Index.jsp:- 
<%@page contentType="text/html" pageEncoding="UTF-8"%> 
<!DOCTYPE html> 
<html> 
    <head> 
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">         <title>JSP Page</title> 
    </head> 
    <body> 
        <h1>Hello World!</h1> 
    </body> 
</html> 
Marks.java:- import javax.servlet.*; import javax.servlet.http.*; import java.io.*; 
public class Marks extends HttpServlet 
{ 
   @Override 
   public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
   { 
      response.setContentType("text/html");       PrintWriter out = response.getWriter(); try      { 
        out.print("<html><body>"); 
        int total=Integer.parseInt(request.getParameter("total"));         int marks=0; 
        for(int i=1; i<=total; i++) 
        { 
           String sel=request.getParameter(new Integer(i).toString()); 
           String ans=request.getParameter("ans"+i);            if (sel.equals(ans)) marks++; 
        } 
         out.println("Total Marks : "+marks); 
         out.print("</body></html>"); 
      } 
      catch(Exception e) 
      { 
        out.println("ERROR "+e.getMessage()); 
      } 
    } 
} 	 
QueAnsDBServlet.java:- import javax.servlet.*; import javax.servlet.http.*; import java.io.*; import java.sql.*; 
public class QueAnsDBServlet extends HttpServlet 
{ 
   public void doGet(HttpServletRequest request, HttpServletResponse response)  throws ServletException, IOException 
  { 
    response.setContentType("text/html");     PrintWriter out = response.getWriter();     try 
    { 
      out.print("<html><body><br>");       out.println("<form method='post' action='Marks'>"); 
      Class.forName("com.mysql.jdbc.Driver"); 
      Connection 
con=DriverManager.getConnection("jdbc:mysql://localhost/queansdb","root","tiger"); 
      Statement st = con.createStatement(); 
      String sql="select * from queans";       ResultSet rs = st.executeQuery(sql);       int i=0; 
      out.print("<center>Online Exam</center>");       while(rs.next()) 
        {            i++; 
          out.print("<br><br><hr>"+rs.getInt(1)+"    "); 
          out.print(rs.getString(2)); 
          out.print("<br><input type=radio name="+i+" value=1>"+rs.getString(3));           out.print("<br><input type=radio name="+i+" value=2>"+rs.getString(4));           out.print("<br><input type=radio name="+i+" value=3>"+rs.getString(5));           out.print("<br><input type=radio name="+i+" value=4>"+rs.getString(6));           String ans="ans"+i; 
          out.println("<br><input type=hidden name="+ans+" value="+rs.getString(7)+">"); 
        } 
          out.println("<br><input type=hidden name=total value="+i+">");           out.println("<input type=submit value=submit>"); 
          out.println("</form>"); 
          out.print("</body></html>"); 
   } 
catch(Exception e) 
   { 
      out.println("ERROR "+e.getMessage()); 
    } 
  } } 
Output: 










  
Q.3 c) Create simple Servlet application to demonstrate NonBlocking Read Operation. 
Index.jsp:- 
<%@page contentType="text/html" pageEncoding="UTF-8"%> 
<!DOCTYPE html> 
<html> 
    <head> 
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">         <title>Non-Blocking Page</title> 
    </head> 
    <body> 
        <a href="NonBlockingServlet"> Non-Blocking </a> 
    </body> 
</html> 
NonBlockingServlet.java:- import java.io.*; 
import java.net.HttpURLConnection; 
import java.net.URL; import javax.servlet.*; 
import javax.servlet.http.*; 
 
public class NonBlockingServlet extends HttpServlet  
{ 
    protected void service(HttpServletRequest req,HttpServletResponse res)throws ServletException,IOException 
    { 
        res.setContentType("text/html");         PrintWriter out = res.getWriter(); 
        String filename = "booklist.txt"; 
        ServletContext c = getServletContext(); 
        InputStream is = c.getResourceAsStream("/"+filename); 
        InputStreamReader isr = new InputStreamReader(is); 
        BufferedReader br = new BufferedReader(isr); 
        String path = 
"http://"+req.getServerName()+":"+req.getServerPort()+req.getContextPath()+"/ReadingNon
BlockingServlet"; 
        out.println("<h1>FileReader/</h1>"); 
        URL url = new URL(path); 
        HttpURLConnection hc = (HttpURLConnection)url.openConnection();         hc.setChunkedStreamingMode(2); 
        hc.setDoOutput(true); 
        hc.connect();         String text = ""; 
        out.println("Reading Started ..."); 
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(hc.getOutputStream())); 
        while((text = br.readLine())!= null) 
        { 
            bw.write(text);             bw.flush(); 
            out.println(text+"<br>"); 
            out.flush(); 
            try 
            { 
                Thread.sleep(1000); 
            } 
            catch(Exception ex) 
            { 
                out.print(ex); 
            } 
        } 
        bw.write("Reading Completed"); 
        bw.flush();         bw.close(); 
    } 
} 
ReadingListener:- import java.io.*; import javax.servlet.*; import javax.servlet.AsyncContext; 
public class ReadingListener implements ReadListener 
{ 
    ServletInputStream input = null; 
    AsyncContext ac = null; 
    ReadingListener(ServletInputStream in,AsyncContext c) 
    {         input = in;         ac = c; 
    } 
    @Override 
    public void onDataAvailable() 
    { 
    } 
    @Override 
    public void onAllDataRead() 
    {ac.complete();}     @Override 
    public void onError(Throwable t) 
    { 
        ac.complete(); 
        t.printStackTrace(); 
    } 
} 
 
ReadingNonBlockingServlet.java:- import java.io.*; import javax.servlet.*; import javax.servlet.http.*; 
import javax.servlet.annotation.WebServlet; 
@WebServlet(name ="ReadingNonBlockingServlet",urlPatterns = {"/ReadingNonBlockingServlet"},asyncSupported = true) public class ReadingNonBlockingServlet extends HttpServlet { 
    @Override 
    protected void service(HttpServletRequest req,HttpServletResponse res)             throws ServletException,IOException{         res.setContentType("text/html");         AsyncContext ac=req.startAsync(); 
        ServletInputStream in=req.getInputStream();         in.setReadListener(new ReadingListener(in,ac)); 
    } } 
Output: 
  
 
