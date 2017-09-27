# utl_20_second_catesian_1x10_millio
Example of a 20 second catesion join of 1 mission x 10 million latittude and longidudes. Uses free SPDE.

    ```  /* T1006840 SAS Forum: 20 second cartesian join of 1 million x 10 million  ```
    ```    ```
    ```     I was able to do a catesian join of the 1 million with the 10 million in about 20 seconds.  ```
    ```    ```
    ```     You need the free SPDE product to partition and handle multiple indexes.  ```
    ```     This functionality is not available without SPDE.  ```
    ```    ```
    ```     Does not exactly solve your problem but might give you some ideas.  ```
    ```     Fortunately the datasets are very small and fit in memory?  ```
    ```    ```
    ```     see  ```
    ```     https://goo.gl/nksn3s  ```
    ```     https://communities.sas.com/t5/General-SAS-Programming/Seeking-Help-With-An-Unusual-Merge/td-p/379606  ```
    ```    ```
    ```     WORKING CODE  ```
    ```    ```
    ```         libname spde spde  ```
    ```         ('c:\wrk\spde_c','d:\wrk\spde_d','e:\wrk\spde_e','f:\wrk\spde_f','g:\wrk\spde_g')  ```
    ```             metapath =('c:\wrk\spde_c\metadata')  ```
    ```             indexpath=(  ```
    ```                   'c:\wrk\spde_c'  ```
    ```                   ,'d:\wrk\spde_d'  ```
    ```                   ,'e:\wrk\spde_e'  ```
    ```                   ,'f:\wrk\spde_f'  ```
    ```                   ,'g:\wrk\spde_g')  ```
    ```    ```
    ```             datapath =(  ```
    ```                   'c:\wrk\spde_c'  ```
    ```                   ,'d:\wrk\spde_d'  ```
    ```                   ,'e:\wrk\spde_e'  ```
    ```                   ,'f:\wrk\spde_f'  ```
    ```                   ,'g:\wrk\spde_g')  ```
    ```             partsize=500m  ```
    ```           ;  ```
    ```    ```
    ```             select  ```
    ```                l.id  ```
    ```               ,r.censusblk  ```
    ```             from  ```
    ```                spde.frosrt as l, spde.tooblksrt as r  ```
    ```             where  ```
    ```                l.lat = r.lat and      /* round all lat long to 1 decimal */  ```
    ```                l.lon = r.lon          /* you could also increase the small 10 million with more lat longs)  ```
    ```           ;quit;                      /* do not be afraid of going from 10 million to a billion */  ```
    ```                                       /* to force exact matches (well under a terabyte           */  ```
    ```            when most gone  ```
    ```            where  ```
    ```                 l.lat between (r.lat - .1) and (r.lat+.1) and  ```
    ```                 l.lon between (r.lon - .1) and (r.lon+.1)  ```
    ```             having  ```
    ```                 geodist(l.lat,l.lon,r.lat,r.lon) = min(geodist(l.lat,l.lon,r.lat,r.lon))  ```
    ```    ```
    ```    ```
    ```  Seeking Help With An Unusual Merge  ```
    ```    ```
    ```  This is small data but big computation problem because the two datasets fit in memory.  ```
    ```  Mutiprogramming, ie grid or reasonable inexpensive workstation(my is about $800 cira 2008)  ```
    ```  should reduce the time.  ```
    ```    ```
    ```  You need to use the free SPDE product to partition and handle multiple indexes.  ```
    ```  This functionality is not available without SPDE.  ```
    ```    ```
    ```  Problem  ```
    ```    ```
    ```  I am seeking help with an unusual merge/join.  ```
    ```  I have two datasets/tables in a Teradata environment, which I am communicating with using EG 7.11.  ```
    ```    ```
    ```  I was ble to join the 1 million with the 10 million in about 30 seconds without multprogramming and  ```
    ```  output almost 80,000,000  obs (because some of my lat longs were in multiple census blocks).  ```
    ```  I had a hard time generating  reasonable input data.  ```
    ```    ```
    ```  Also I rounded the lat longs to 1 decimal place whch meant there had to be a lot of  ```
    ```  exact lat long matches giver 10 million lat longs.  ```
    ```    ```
    ```  My take is if you are willing to round to one decimal you can at least cut the 1 million  ```
    ```  table by removing the exact matches and then run again with the non matches.  ```
    ```    ```
    ```    ```
    ```  HAVE  ```
    ```  ====  ```
    ```    ```
    ```  WORK.FRO total obs=1,000,000  ```
    ```    ```
    ```      Obs    ID     LAT      LON  ```
    ```    ```
    ```        1     1    -44.3    -24.2  ```
    ```        2     2    -45.0    -25.0  ```
    ```        3     3    -44.8    -25.2  ```
    ```        4     4    -44.6    -24.4  ```
    ```    ```
    ```    ```
    ```  WORK.TOO total obs=10,000,000  ```
    ```    ```
    ```       Obs     LAT      LON  ```
    ```    ```
    ```        28    -44.2    -24.8  ```
    ```        29    -44.5    -24.6  ```
    ```        30    -44.7    -24.8  ```
    ```        31    -45.0    -25.2  ```
    ```        32    -44.6    -25.3  ```
    ```        33    -44.8    -25.3  ```
    ```        34    -44.1    -25.0  ```
    ```        35    -44.6    -25.2  ```
    ```        36    -44.7    -25.1  ```
    ```        37    -44.2    -25.1  ```
    ```        38    -45.0    -25.2  ```
    ```        39    -44.2    -25.3  ```
    ```        40    -44.8    -25.0  ```
    ```    ```
    ```  *                _              _       _  ```
    ```   _ __ ___   __ _| | _____    __| | __ _| |_ __ _  ```
    ```  | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |  ```
    ```  | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |  ```
    ```  |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|  ```
    ```  ;  ```
    ```    ```
    ```  proc datasets lib=work kill;  ```
    ```  run;quit;  ```
    ```    ```
    ```  proc datasets lib=spde kill;  ```
    ```  run;quit;  ```
    ```    ```
    ```  libname spde spde  ```
    ```   ('c:\wrk\spde_c','d:\wrk\spde_d','e:\wrk\spde_e','f:\wrk\spde_f','g:\wrk\spde_g')  ```
    ```      metapath =('c:\wrk\spde_c\metadata')  ```
    ```      indexpath=(  ```
    ```            'c:\wrk\spde_c'  ```
    ```            ,'d:\wrk\spde_d'  ```
    ```            ,'e:\wrk\spde_e'  ```
    ```            ,'f:\wrk\spde_f'  ```
    ```            ,'g:\wrk\spde_g')  ```
    ```    ```
    ```      datapath =(  ```
    ```            'c:\wrk\spde_c'  ```
    ```            ,'d:\wrk\spde_d'  ```
    ```            ,'e:\wrk\spde_e'  ```
    ```            ,'f:\wrk\spde_f'  ```
    ```            ,'g:\wrk\spde_g')  ```
    ```      partsize=500m  ```
    ```  ;  ```
    ```    ```
    ```  /*  ```
    ```  NOTE: Libref SPDE was successfully assigned as follows:  ```
    ```        Levels:           5  ```
    ```        Engine(1):        SPDE  ```
    ```        Physical Name(1): c:\wrk\spde_c\  ```
    ```        Engine(2):        SPDE  ```
    ```        Physical Name(2): d:\wrk\spde_d\  ```
    ```        Engine(3):        SPDE  ```
    ```        Physical Name(3): e:\wrk\spde_e\  ```
    ```        Engine(4):        SPDE  ```
    ```        Physical Name(4): f:\wrk\spde_f\  ```
    ```        Engine(5):        SPDE  ```
    ```        Physical Name(5): g:\wrk\spde_g\  ```
    ```  */  ```
    ```    ```
    ```    ```
    ```  data fro(drop=latx lonx);  ```
    ```     retain id 0;  ```
    ```     call streaminit(5731);  ```
    ```     do latx=-45 to 45 by .1;  ```
    ```       do lonx=-25 to -175 by -.1;  ```
    ```         id=id+1;  ```
    ```         lat=round(latx + rand("uniform"),.1);  ```
    ```         lon=round(lonx + rand("uniform"),.1);  ```
    ```         if id=1000001 then stop;  ```
    ```         output;  ```
    ```       end;  ```
    ```     end;  ```
    ```  run;quit;  ```
    ```    ```
    ```  /*  ```
    ```  NOTE: The data set WORK.FRO has 1,000,000 observations and 3  ```
    ```  */  ```
    ```    ```
    ```  proc sort data=fro out=spde.frosrt(index=(lat lon)) noequals;  ```
    ```  by lat lon;  ```
    ```  run;quit;  ```
    ```    ```
    ```  data too (drop=latx lonx i id);  ```
    ```     retain id 0;  ```
    ```     call streaminit(5733);  ```
    ```     do latx=-45 to 45 by .1;  ```
    ```       do lonx=-25 to -175 by -.1;  ```
    ```         do i=1 to 8;  ```
    ```           id=id+1;  ```
    ```           lat=round(latx + rand("uniform"),.1);  ```
    ```           lon=round(lonx + rand("uniform"),.1);  ```
    ```           if id=10000001 then stop;  ```
    ```           output;  ```
    ```         end;  ```
    ```       end;  ```
    ```     end;  ```
    ```  run;quit;  ```
    ```    ```
    ```  /*  ```
    ```  NOTE: The data set WORK.FRO has 1000000 observations and 3  ```
    ```  */  ```
    ```    ```
    ```  proc sort data=too out=toosrt noequals;  ```
    ```  by lat lon;  ```
    ```  run;quit;  ```
    ```    ```
    ```  %let letters=%str(  ```
    ```  "A" ,"B" ,"C" ,"D" ,"E" ,"F" ,"G" ,"H" ,"I" ,"J" ,"K" ,"L"  ```
    ```  ,"M" ,"N" ,"O" ,"P" ,"Q" ,"R" ,"S" ,"T" ,"U" ,"V" ,"W" ,"X"  ```
    ```  ,"Y" ,"Z");  ```
    ```    ```
    ```  data tooblk;  ```
    ```    retain cnt -1 idxltr idxnum 0 ltr num;  ```
    ```    array ltrs[26] $1 (&letters);  ```
    ```    array nums[10] (&numbers);  ```
    ```    set toosrt(obs=10000000);  ```
    ```    cnt=cnt+1;  ```
    ```    set toosrt;  ```
    ```    num=put(mod(cnt,999),z3.);  ```
    ```    if mod(cnt,400000)=0 then do;  ```
    ```       idxltr=idxltr+1;  ```
    ```       ltr=ltrs[idxltr];  ```
    ```    end;  ```
    ```    censusblk=cats(ltr,num);  ```
    ```    keep lat lon censusblk;  ```
    ```  run;quit;  ```
    ```    ```
    ```  proc sort data=tooblk out=spde.tooblksrt(index=(latlon=(lat lon))) noequals;  ```
    ```  by lat lon;  ```
    ```  run;quit;  ```
    ```    ```
    ```  NOTE: PROCEDURE SORT used (Total process time):  ```
    ```        real time           1:03.70  ```
    ```    ```
    ```  *          _       _   _  ```
    ```   ___  ___ | |_   _| |_(_) ___  _ __  ```
    ```  / __|/ _ \| | | | | __| |/ _ \| '_ \  ```
    ```  \__ \ (_) | | |_| | |_| | (_) | | | |  ```
    ```  |___/\___/|_|\__,_|\__|_|\___/|_| |_|  ```
    ```  ;  ```
    ```    ```
    ```  proc sql;  ```
    ```    create  ```
    ```       table spde.want as  ```
    ```    select  ```
    ```       l.id  ```
    ```      ,r.censusblk  ```
    ```    from  ```
    ```       spde.frosrt as l, spde.tooblksrt as r  ```
    ```    where  ```
    ```       l.lat = r.lat and  ```
    ```       l.lon = r.lon  ```
    ```  ;quit;  ```
    ```    ```
    ```    ```
    ```   NOTE: Table SPDE.WANT created, with 7958679 rows and 2 columns.  ```
    ```    ```
    ```   4181!  quit;  ```
    ```   NOTE: PROCEDURE SQL used (Total process time):  ```
    ```         real time           9.02 seconds  ```
    ```         user cpu time       3.55 seconds  ```
    ```         system cpu time     7.78 seconds  ```
    ```         memory              7192.39k  ```
    ```         OS Memory           29160.00k  ```
    ```         Timestamp           07/27/2017 04:40:06 PM  ```
    ```         Step Count                        550  Switch Count  1580  ```
    ```    ```
    ```    ```
    ```    ```
    ```    ```
    ```    ```
