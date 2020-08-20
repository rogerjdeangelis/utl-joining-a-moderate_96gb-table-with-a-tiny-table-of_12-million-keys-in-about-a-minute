# utl-joining-a-moderate_96gb-table-with-a-tiny-table-of_12-million-keys-in-about-a-minute
Joining a moderate_96gb table with a tiny table of_12 million keys in about a minute 
    Joining a moderate_96gb table with a tiny table of_12 million keys in about a minute                                                
                                                                                                                                        
    The join took 85 seconds. I can reduce this time using multi-tasking , but it is not worth it.                                      
                                                                                                                                        
    github                                                                                                                              
    https://tinyurl.com/y5nmkama                                                                                                        
    https://github.com/rogerjdeangelis/utl-joining-a-moderate_96gb-table-with-a-tiny-table-of_12-million-keys-in-about-a-minute         
                                                                                                                                        
    The op states that the table is read only, but is updated frequently?                                                               
    You need to use a snapshot or you will never be able to verify your prograns or get the same answer twice.                          
                                                                                                                                        
    I cretaed an index since want only interested 0.5% of records in the moderate table.                                                
    This is what inexes are for.                                                                                                        
                                                                                                                                        
    related to                                                                                                                          
    https://tinyurl.com/y6hjuqrj                                                                                                        
    https://stackoverflow.com/questions/47168148/sql-sas-best-performance-for-selecting-from-big-table-2bn-rows                         
                                                                                                                                        
    It is very unusual for a moderate sized table, 96gb, not have locality of reference.                                                
    Often there are natuaral ways to partition a moderate table, ie state, region, zip, time.                                           
                                                                                                                                        
    The moderate data table  I created has sequenced but spaced datetime. Sort of like subscrition data.                                
    Even if the dates are sequenced within another variable like state, you will have locality of reference.                            
                                                                                                                                        
    I have seen oracle add a temporary index on the fly to speed up a join.                                                             
    Even though the op did not want an index. I created on in about 10 minutes.                                                         
    After that the join took about a minute.                                                                                            
    SPDE indexing is multi-theaded,                                                                                                     
                                                                                                                                        
                                                                                                                                        
    /*                   _                                                                                                              
    (_)_ __  _ __  _   _| |_                                                                                                            
    | | `_ \| `_ \| | | | __|                                                                                                           
    | | | | | |_) | |_| | |_                                                                                                            
    |_|_| |_| .__/ \__,_|\__|                                                                                                           
            |_|                                                                                                                         
    */                                                                                                                                  
                                                                                                                                        
                                                                                                                                        
    libname spde spde                                                                                                                   
       ('f:\spde_f','m:\spde_m')                                                                                                        
         metapath =('c:\spde_c\metadata')                                                                                               
        indexpath=(                                                                                                                     
              ,'f:\spde_f'                                                                                                              
              ,'m:\spde_m')                                                                                                             
        datapath =(                                                                                                                     
              ,'f:\spde_f'                                                                                                              
              ,'m:\spde_m')                                                                                                             
        partsize=1000m                                                                                                                  
    ;                                                                                                                                   
                                                                                                                                        
    proc datasets lib=spde kill;                                                                                                        
    run;quit;                                                                                                                           
                                                                                                                                        
    data spde.big(index=(dteTym/unique));                                                                                               
      retain dtetym '01JAN60:00:00:00'dt;                                                                                               
      call streaminit(8765);                                                                                                            
      set sashelp.class(obs=10);                                                                                                        
      do i=1 to 24*8333333;                                                                                                             
         dteTym=dteTym + abs(int(rand('normal',10,1))) + 1;                                                                             
         output;                                                                                                                        
      end;                                                                                                                              
      drop i;                                                                                                                           
    run;quit;                                                                                                                           
                                                                                                                                        
    NOTE: The data set SPDE.BIG has 1,999,999,920 observations and 6 variables.                                                         
    NOTE: Simple index DTETYM has been defined.                                                                                         
    NOTE: DATA statement used (Total process time):                                                                                     
                                                                                                                                        
          real time           10:44.52   ** MUTI-THREADED                                                                               
          user cpu time       23:35.25   ================                                                                               
          system cpu time     3:33.48                                                                                                   
          memory              1117088.18k                                                                                               
          OS Memory           1305764.00k                                                                                               
          Timestamp           08/19/2020 07:16:16 PM                                                                                    
                                                                                                                                        
    data work.ltl;                                                                                                                      
      set spde.big(keep=dteTym);;                                                                                                       
      do i=1 to 2e9 by 167;                                                                                                             
         output;                                                                                                                        
      end;                                                                                                                              
      keep dteTym;                                                                                                                      
      stop;                                                                                                                             
    run;quit;                                                                                                                           
                                                                                                                                        
    /*           _               _                                                                                                      
      ___  _   _| |_ _ __  _   _| |_                                                                                                    
     / _ \| | | | __| `_ \| | | | __|                                                                                                   
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                    
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                   
                    |_|                                                                                                                 
    */                                                                                                                                  
                                                                                                                                        
    Data Set Name        WORK.WORK                     Observations          11,976,048                                                 
    Member Type          DATA                          Variables             6                                                          
    Engine               V9                            Indexes               0                                                          
    Created              08/19/2020 19:20:45           Observation Length    48                                                         
                                                                                                                                        
    NOTE: PROCEDURE SQL used (Total process time):                                                                                      
          real time           1:23.12                                                                                                   
          user cpu time       45.12 seconds                                                                                             
          system cpu time     38.03 seconds                                                                                             
                                                                                                                                        
     Variables in Creation Order                                                                                                        
                                                                                                                                        
    #    Variable    Type    Len                                                                                                        
                                                                                                                                        
    1    DTETYM      Num       8                                                                                                        
    2    NAME        Char      8                                                                                                        
    3    SEX         Char      1                                                                                                        
    4    AGE         Num       8                                                                                                        
    5    HEIGHT      Num       8                                                                                                        
                                                                                                                                        
                                                                                                                                        
    /*                                                                                                                                  
     _ __  _ __ ___   ___ ___  ___ ___                                                                                                  
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                                                                                 
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                                                 
    | .__/|_|  \___/ \___\___||___/___/                                                                                                 
    |_|                                                                                                                                 
    */                                                                                                                                  
                                                                                                                                        
    proc sql;                                                                                                                           
      create                                                                                                                            
         table work as                                                                                                                  
      select                                                                                                                            
         big.*                                                                                                                          
      from                                                                                                                              
         spde.big as big, work.ltl as ltl                                                                                               
      where                                                                                                                             
         big.dteTym  =  ltl.dteTym                                                                                                      
    ;quit;                                                                                                                              
                                                                                                                                        
    NOTE: PROCEDURE SQL used (Total process time):                                                                                      
          real time           1:25.30                                                                                                   
          user cpu time       42.78 seconds                                                                                             
          system cpu time     42.10 seconds                                                                                             
          memory              8363.75k                                                                                                  
          OS Memory           52032.00k                                                                                                 
          Timestamp           08/20/2020 08:06:32 AM                                                                                    
          Step Count                        576  Switch Count  5                                                                        
                                                                                                                                        
                                                                                                                                        
