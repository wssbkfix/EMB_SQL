# Embedded SQL - Overview
When IBM introduced SQL to the iSeries and RPG, embedded SQL seemed to be the easy path to take. When I got started in SQL years ago on the iSeries, there seemed to be 3 options you could take. 
1- Call Stored procedures. 
2- use CLI (basically a set of API that does about anything SQL you would want to do. 
3- Use Embedded SQL. 

Many purest feel that stored procedures are the way to go. They have many benefits such as being the closed to the standard SQL language. If you have good SQL tools, they are realtively easy to code in. If you are green screening it, not so easy. 

CLI (Call Level Interface is a suite of API that allow a program to interface with database via a API call. This has the highest learning curve but if you have worked in C or called C programs via RPG your taks is easier. The one limit is that it only does dynamic SQL. Whatever performance you might loose with dynamic SQL you will gain some back because of the use of Service programs instead of dynamic calls. 

Finally there is embedded SQL. For me this is the easiest to get started with. It still comes with its challenges. There are two phases: The pre-compiler stage, and the compile stage. The compile stage takes the original source and generates a source with extra SQL defintions and calls to SQL programs. This SQL-ready source is then compiled by the standard RPG compiler. This process is invoked by using the CRTSQLRPGI command. You can choose to create a module or a program from this command. I used embedded SQL for years without paying attention to what pre-compiled code  
