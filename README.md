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
## Compare the original code to the pre-compile code. 
There are two ways to see this. The first is covered here. One just needs to look at the generated compiler listing. This can be done if the pre-compile does not fail. 
