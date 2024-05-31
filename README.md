# Embedded SQL - Overview
When IBM introduced SQL to the iSeries and RPG, embedded SQL seemed to be the easy path to take. When I got started in SQL years ago on the iSeries, there seemed to be 3 options you could take. 
1- Call Stored procedures. 
2- use CLI (basically a set of API that does about anything SQL you would want to do. 
3- Use Embedded SQL. 

Many purest feel that stored procedures are the way to go. They have many benefits such as being the closed to the standard SQL language. If you have good SQL tools, they are realtively easy to code in. If you are green screening it, not so easy. 

CLI (Call Level Interface is a suite of API that allow a program to interface with database via a API call. This has the highest learning curve but if you have worked in C or called C programs via RPG your taks is easier. The one limit is that it only does dynamic SQL. Whatever performance you might loose with dynamic SQL you will gain some back because of the use of Service programs instead of dynamic calls. 

Finally there is embedded SQL. For me this is the easiest to get started with. It still comes with its challenges. There are two phases: The pre-compiler stage, and the compile stage. The compile stage takes the original source and generates a source with extra SQL defintions and calls to SQL programs. This SQL-ready source is then compiled by the standard RPG compiler. This process is invoked by using the CRTSQLRPGI command. You can choose to create a module or a program from this command. I used embedded SQL for years without paying attention to what pre-compiled code. I got curious and want to dig into what the pre-compile code. With a little effort I got the details of what is going on. I created a simple program that used a table function to read data. I compiled the program and looked at the compiler listing. The listing showed me the following 
1- The variable, and data structure definitions added for SQL 
2- The generated calculation specifications. 
3- Parts of the original source that was remarked out.  It is interesting that it was not deleted but just remarked out. 

The program I coded was in free form. (Why you code any other way!!). I kept it simple so any actions would be easy to see. 

# The Code 
1- ReadIFS3.sqlrpgle This is the program that everything is based off of. It is a simple one SQL statement program that fills up the fileOneValuesRec data structure. The data structure has 6 fields which are attributes of a file in the IFS. 
2  The Table function QSYS2.IFS_OBJECT_STATISTICS. This function reads the attributes of a stream file in the IFS. 
   There a three parameters in the table function which are self explanatory. In this case I wanted a specific file: specified in the START_PATH_NAME. I did not want any subfolders so a NO is spefied after SUBTREE_DIRECTORIES. The OMIT_LIST is included because I have
   a lot of node.js files I want to avoid. The result should be a view of the attributes of just one file that is specified in the fileValuePath variable. 
## A view of the compiler listing 
There are two ways to see this. The first is covered here. One just needs to look at the generated compiler listing. This can be done if the pre-compile does not fail. 

A compiler listing has been supplied ReadIFS3.sqlrpgle_PreComp.txt. You view this file. I recommend viewing it raw to remove the extra line numbers. All of the source is line numbered already. The review of the source is in the following parts. 
1- Variable and data structure definitions 
2- Prototype definitions. 
3- Constant definitions 
4- Calculation spects. 

### variable and data structure definition 
If you scroll down to the first page of the compiler list (see sample below )

     20  //--------------------------------------------------------------------                                           
     21 // main entry  ;                                                                                                  
     22 //--------------------------------------------------------------------                                            
     23                                                                                                                   
     24        /SET CCSID(*CHAR:*JOBRUNMIX)                                                                               
     25        // SQL COMMUNICATION AREA                                                //SQL                             
     26        DCL-DS SQLCA;                                                            //SQL                             
     27          SQLCAID CHAR(8) INZ(X'0000000000000000');                              //SQL                             
     28           SQLAID CHAR(8) OVERLAY(SQLCAID);                                      //SQL                             
     29          SQLCABC INT(10);                                                       //SQL                             
     30           SQLABC BINDEC(9) OVERLAY(SQLCABC);                                    //SQL                             
     31          SQLCODE INT(10);                                                       //SQL                             


From source line 24 to source line 79 the SQLCA data structure is defined. 

Then lines 91 to 103 have a unlabled data structure. Don't know why IBM went away from free format here but the did. At least the documented each field. The data structure is used to store the sql statement and have place holders for the fields retrieved. Note that the comments for SQL_00007 through SQL_00012  match field names in the data structure after the INTO clause of the select statement. 
After that, lines 81 through 88 defined the constants. 

Lines 81 through 88 have the constants. 

### calculation specs 
Two variables are populated, SQL_00005 and SQL00006. These are the host variable values. There ae only two values passed by parameter, SQL_00001 (Length of header), and the SQLCA). This means not all values are passed via parameters. Perhaps base pointers are used sinc the address is known of the data structure because the SQL_00000 is passed. 

Because of the Constaints, actual program called is 'QSYS/QSQROUTE'. You can find that program and display it's attributes.  

The calculations specs. 
Lines 

    120           SQL_00005 = FILEVALUEPATH;                                            //SQL      
    121           SQL_00006 = WSOMITLIST;                                               //SQL     
    122           SQLER6 = -4;                                                          //SQL  
    123           SQLROUTE_CALL(                                                        //SQL  
    124                SQLCA                                                            //SQL   
    125              : SQL_00000                                                        //SQL    
    126           );                        

### making it easier 
You may have noticed that all of the generated code has //SQL at the end of each line. You can use a find function to get all of the generated code. As you probably already noticed, the code that was disabled has the "//" in the first columns. With RDI or Visual Studio Code this makes those lines stand out. 

### Final notes on the compiler listing
You may wonder if you can just go ahead and compile this. I did not have any luck with it, but there is another approach that will give us a pre-compile source that can be compiled. 

# Running the precompiiler without a compile 



