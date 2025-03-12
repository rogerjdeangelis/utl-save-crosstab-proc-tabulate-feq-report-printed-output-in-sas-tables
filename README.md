# utl-save-crosstab-proc-tabulate-feq-report-printed-output-in-sas-tables
Save crosstab proc tabulate feq report printed output in sas tables 
    %let pgm=utl-save-crosstab-proc-tabulate-feq-report-printed-output-in-sas-tables;

    %stop_submission;

    Save crosstab proc tabulate feq report printed output in sas tables

    SOAPBOX ON

       The orginal reason for ods was to produce datasets from the displayed
       printed output. However many procs failed tp produce output
       that matches the display. Notable are
       proc tabulate and  Proc freq. This post tries to fill the gap by
       structuring the proc printto output into a delimited file and
       importing the file into q dataset. One notable exception is "ods exce'
       which does produce sheets that look like proc output.

    SOAPBOX ON

    Best with classic 1980s dms editor?
    May give different results interactively in editors other than the classic 1980s editor

              1 proc report

              2 proc corresp

              3 proc tabulate read prinnto
                Can handle som comples tabulates?)
                Bartosz Jablonski
                yabwon@gmail.com
                note: recently updated utl_odstab
                https://tinyurl.com/y9nfugth

              4 proc report read prinnto

              5 ods excel and back (r and sas)
                see https://tinyurl.com/s7pu5m4e

              6 proc freq

              7 proc sql (with & without sas macro arrays)

              8 r sql (with & without r vector arrays)

              9 python sql

             10 means then datasets

             11 more complex tabulate

             12 related repos

             13 ods macros

    github
    https://tinyurl.com/mrx5btmz
    https://github.com/rogerjdeangelis/utl-save-crosstab-proc-tabulate-feq-report-printed-output-in-sas-tables
                                                                           
    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    I WANT TO SAVE THIS PROC TABULATE DISPLAY IN A DATASET

    proc tabulate data=sd1.have;
    class ay am per;
    table ay*am, per*amt*(sum) /rts=13;
    var amt;
    run;quit;

     ----------------------------------------------------------------
    |           |                        PER                        |
    |           |---------------------------------------------------|
    |           |     6      |     7      |     8      |     9      |
    |           |------------+------------+------------+------------|
    |           |    AMT     |    AMT     |    AMT     |    AMT     |
    |           |------------+------------+------------+------------|
    |           |    Sum     |    Sum     |    Sum     |    Sum     |
    |-----------+------------+------------+------------+------------|
    |AY   |AM   |            |            |            |            |
    |-----+-----|            |            |            |            |
    |2018 |2    |       22.00|       24.00|           .|           .|
    |     |-----+------------+------------+------------+------------|
    |     |3    |       20.00|       24.00|       24.00|           .|
    |     |-----+------------+------------+------------+------------|
    |     |4    |           .|           .|           .|       84.00|
    |-----+-----+------------+------------+------------+------------|
    |2019 |3    |       60.00|       24.00|           .|           .|
    |     |-----+------------+------------+------------+------------|
    |     |4    |      100.00|       22.00|       22.00|           .|
    -----------------------------------------------------------------


    More interesting dataset from tabulate display
    https://tinyurl.com/5n8yef2t
    https://github.com/rogerjdeangelis/utl-create-a-crosstab-dataset-instead-of-a-listing-from-proc-tabulate

    https://communities.sas.com/t5/SAS-Programming/PROC-TABULATE-save-export-SAS-report-to-SAS-dataset/m-p/878317#M346996

    proc datasets lib=work kill nodetails nolist;
    run;quit;


    /**************************************************************************************************************************/
    /*         INPUT          |      PROCESS                                   |                                              */
    /*                        |                                                |                                              */
    /*   DATA SORTED FOR      |   SUMBY AY AND AM THEN USE                     |                                              */
    /*   DOCUMENTATION ONLY   |   PER FOR COLUMN NAME                          |                                              */
    /*   SOLUTIONS OK NOTSORTD|                                                |                                              */
    /*                        |                                                | WORK.WANT                                    */
    /*         INPUT          |SUM   SUM   SUM   SUM                           |                                              */
    /*                        |AMT6  AMT7  AMT8  AMT9                          |                                              */
    /*    AY  AM PER AMT      |per=6 per=7 per=8 per=9                         |   AY  AM    AMT1    AMT2    AMT3    AMT4     */
    /*                        |                                                |                                              */
    /*   2018  2  6   11      |                                                |  2018  2      22     24       .       .      */
    /*   2018  2  6   11  22  |                                                |  2018  3      20     24      24       .      */
    /*   2018  2  7   12      |                                                |  2018  4       .      .       .      84      */
    /*   2018  2  7   12  24  |  22   24    ,     ,                            |  2019  3      60     24       .       .      */
    /*                        |                                                |  2019  4     100     22      22      22      */
    /*   2018  3  6   10      |                                                |                                              */
    /*   2018  3  6   10  20  |                                                |                                              */
    /*   2018  3  7   12      |                                                |                                              */
    /*   2018  3  7   12  24  |                                                |                                              */
    /*   2018  3  8   12      |                                                |                                              */
    /*   2018  3  8   12  24  |  20   24    24     .                           |                                              */
    /*                        |                                                |                                              */
    /*   2018  4  9   42      |                                                |                                              */
    /*   2018  4  9   42  84  |   .    .     .    84                           |                                              */
    /*                        |                                                |                                              */
    /*   2019  3  6   30      |                                                |                                              */
    /*   2019  3  6   30  60  |                                                |                                              */
    /*   2019  3  7   12      |                                                |                                              */
    /*   2019  3  7   12  24  |  60   24    .      .                           |                                              */
    /*                        |                                                |                                              */
    /*   2019  4  6   50      |                                                |                                              */
    /*   2019  4  6   50 100  |                                                |                                              */
    /*   2019  4  7   11      |                                                |                                              */
    /*   2019  4  7   11  22  |                                                |                                              */
    /*   2019  4  8   11      |                                                |                                              */
    /*   2019  4  8   11  22  |                                                |                                              */
    /*   2019  4  9   11      |                                                |                                              */
    /*   2019  4  9   11  22  | 100   22    22    22                           |                                              */
    /*                        |                                                |                                              */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*  NOT SORTED INPUT      | 1 PROC REPORT (CAN OFTEN MATCH TABULATE)       |                                              */
    /*   options              | ==============                                 |  AY     AM    AMT1    AMT2    AMT3    AMT4   */
    /*   validvarname=upcase; |                                                |                                              */
    /*   libname sd1 "d:/sd1";| proc report data=sd1.have                      | 2018     2      22     24       .       .    */
    /*   data sd1.have;       |  out=want (drop=_break_                        | 2018     3      20     24      24       .    */
    /*   input ay am per amt; |   rename=(                                     | 2018     4       .      .       .      84    */
    /*   cards4;              |    %utl_renamel(                               | 2019     3      60     24       .       .    */
    /*   2018 2 6 11          |       old=_c3_ _C4_ _c5_ _c6_,                 | 2019     4     100     22      22      22    */
    /*   2018 2 6 11          |       new=amt1 amt2 amt3 amt4)));              |                                              */
    /*   2018 2 7 12          | cols ay am  per, amt ;                         |                                              */
    /*   2018 2 7 12          | define ay  / group;                            |                                              */
    /*   2018 3 8 12          | define am  / group;                            |                                              */
    /*   2018 3 8 12          | define per  / across ;                         |                                              */
    /*   2018 4 9 42          | define amt / analysis sum ;                    |                                              */
    /*   2018 4 9 42          | run;quit;                                      |                                              */
    /*   2018 3 6 10          |-----------------------------------------------------------------------------------------------*/
    /*   2018 3 6 10          |                                                |                                              */
    /*   2018 3 7 12          | 2 PROC CORRESP                                 | WORK.WANT_COR UNFORTUNATELY MISSING=0        */
    /*   2018 3 7 12          | ==============                                 |                                              */
    /*   2019 3 6 30          |                                                |   AY  LABEL AMT1    AMT2    AMT3    AMT4     */
    /*   2019 3 6 30          | UNFORTUNATELY MISSING=0                        |                                              */
    /*   2019 3 7 12          |                                                |  2018   2     22     24       0       0      */
    /*   2019 3 7 12          | ods exclude all;                               |  2018   3     20     24      24       0      */
    /*   2019 4 6 50          | ods output observed=want_cor                   |  2018   4      0      0       0      84      */
    /*   2019 4 6 50          |   (rename=(_6-_9=amt1-amt4)                    |  2019   3     60     24       0       0      */
    /*   2019 4 7 11          |    drop=sum                                    |  2019   4    100     22      22      22      */
    /*   2019 4 7 11          |    where=(label ne "Sum"));                    |                                              */
    /*   2019 4 8 11          | proc corresp data=sd1.have                     |                                              */
    /*   2019 4 8 11          |   dim=1 observed;                              |                                              */
    /*   2019 4 9 11          | by ay;                                         |                                              */
    /*   2019 4 9 11          | table am,  per;                                |                                              */
    /*   ;;;;                 | weight amt;                                    |                                              */
    /*   run;quit;            | run;quit;                                      |                                              */
    /*                        | ods select all;                                |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        | 3 PROC TABULATE READ PRINNTO                   | SD1.WANT_TB                                  */
    /*                        | ============================                   |                                              */
    /*                        |                                                |   AY   AM    AMT1    AMT2    AMT3    AMT4    */
    /*                        | options FORMCHAR='|----|+|---+=|-/\<>*';       |                                              */
    /*                        | %utl_odstab(setup);                            |  2018   2      22     24       .       .     */
    /*                        | proc tabulate data=sd1.have;                   |     .   3      20     24      24       .     */
    /*                        | title"|AY|AM|AMT1|AMT2|AMT3|AMT4|";            |     .   4       .      .       .      84     */
    /*                        |   class ay am per;                             |  2019   3      60     24       .       .     */
    /*                        |   table ay=''*am=''                            |     .   4     100     22      22      22     */
    /*                        |    ,per=''*amt=''*(sum='') /rts=13;            |                                              */
    /*                        |   var amt;                                     |                                              */
    /*                        | run;quit;                                      |                                              */
    /*                        | /*----  change datarow? ----*/                 |                                              */
    /*                        | %utl_odstab(outdsn=sd1.want_tb,datarow=1);     |                                              */
    /*                        |                                                |                                              */
    /*                        | * restore formchar;                            |                                              */
    /*                        | options FORMCHAR='|----|+|---+=|-/\<>*';       |                                              */
    /*                        | run;quit;                                      |                                              */
    /*                        |                                                |                                              */
    /*                        | INTERMDIATE PROC PRINNTO OUTPUT                |                                              */
    /*                        |                                                |                                              */
    /*                        | |AY|AM|AMT1|AMT2|AMT3|AMT4|                    |                                              */
    /*                        | |2018 |2| 22.00|24.00|    .|    .|             |                                              */
    /*                        | |     |3| 20.00|24.00|24.00|    .|             |                                              */
    /*                        | |     |4|     .|    .|    .|84.00|             |                                              */
    /*                        | |2019 |3| 60.00|24.00|    .|    .|             |                                              */
    /*                        | |     |4|100.00|22.00|22.00|22.00|             |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        | 4 PROC REPORT READ PRINNTO                     |                                              */
    /*                        | ============================                   |                                              */
    /*                        |                                                |                                              */
    /*                        | options missing=".";                           |                                              */
    /*                        | proc report data=sd1.have                      | SD1.WANT_RP                                  */
    /*                        |   nowd missing                                 |                                              */
    /*                        |   noheader formchar="|" box;                   |   AY     AM    AMT1    AMT2    AMT3    AMT4  */
    /*                        | title1 "|AY|AM|AMT1|AMT2|AMT3|AMT4|";          |                                              */
    /*                        | cols ay am  per, amt ;                         |  2018     2      22     24       .       .   */
    /*                        | define ay  / group ;                           |     .     3      20     24      24       .   */
    /*                        | define am  / group;                            |     .     4       .      .       .      84   */
    /*                        | define per  / across ;                         |  2019     3      60     24       .       .   */
    /*                        | define amt / analysis sum missing;             |     .     4     100     22      22      22   */
    /*                        | run;quit;                                      |                                              */
    /*                        | %utl_odsrpt(outdsn=sd1.want_rp);               |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        | 5 ODS EXCEL AND BACK (R AND SAS HAS LEGS)      | R                                            */
    /*                        | =========================================      |                                              */
    /*                        |                                                | want                                         */
    /*                        |  ods excel file="d:/xls/tab.xlsx";             |   AY AM   AMT6  AMT7  AMT8  AMT9             */
    /*                        |  ods excel options(sheet_name="have");         | 2018  2  22.00 24.00     .     .             */
    /*                        |  proc tabulate data=sd1.have;                  |   NA  3  20.00 24.00 24.00     .             */
    /*                        |    class ay am per;                            |   NA  4      .     .     . 84.00             */
    /*                        |    table ay=''*am=''                           | 2019  3  60.00 24.00     .     .             */
    /*                        |     ,per=''*amt=''*(sum='') /rts=13;           |   NA  4 100.00 22.00 22.00 22.00             */
    /*                        |    var amt;                                    |                                              */
    /*                        |  run;quit;                                     | SAS SD1.WANT                                 */
    /*                        |  ods excel close;                              |                                              */
    /*                        |                                                | ROWNAMES AY  AM AMT6    AMT7   AMT8   AMT9   */
    /*                        |  %utl_rbeginx;                                 |                                              */
    /*                        |  parmcards4;                                   |     1   2018  2 22.00   24.00  .      .      */
    /*                        |  library(openxlsx)                             |     2      .  3 20.00   24.00  24.00  .      */
    /*                        |  source("c:/oto/fn_tosas9x.R")                 |     3      .  4 .       .      .      84.00  */
    /*                        |  wb<-loadWorkbook("d:/xls/tab.xlsx")           |     4   2019  3 60.00   24.00  .      .      */
    /*                        |  want<-have<-read.xlsx(wb,"have")              |     5      .  4 100.00  22.00  22.00  22.00  */
    /*                        |  want                                          |                                              */
    /*                        |  colnames(want)=c("AY","AM"                    |                                              */
    /*                        |     ,"AMT6","AMT7","AMT8","AMT9")              |                                              */
    /*                        |  want                                          |                                              */
    /*                        |  fn_tosas9x(                                   |                                              */
    /*                        |        inp    = want                           |                                              */
    /*                        |       ,outlib ="d:/sd1/"                       |                                              */
    /*                        |       ,outdsn ="want"                          |                                              */
    /*                        |       )                                        |                                              */
    /*                        |  ;;;;                                          |                                              */
    /*                        |  %utl_rendx;                                   |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        |  6 proc freq                                   | WORK.WANT_FREQ  (extra variables             */
    /*                        |  ============                                  | (are used for more complex reports)          */
    /*                        |                                                |                                              */
    /*                        |  data hav/view=hav;                            |                                              */
    /*                        |   length ay_am $8;                             | ROWNAM LEVEL VAR1   AMT1 AMT2 AMT3 AMT4 SUM  */
    /*                        |   set sd1.have;                                |                                              */
    /*                        |   ay_am=cats(ay,'_',am);                       | COUNT    .   Frequency  6   7    8    9  Tot */
    /*                        |  run;quit;                                     | PERCENT  .   2018_2    22  24    0    0  46  */
    /*                        |                                                | ROW PCT  .   2018_3    20  24   24    0  68  */
    /*                        |  %utl_odsfrq(setup);                           | COL PCT  .   2018_4     0   0    0   84  84  */
    /*                        |  options formchar="|";                         | COUNT    .   2019_3    60  24    0    0  84  */
    /*                        |  proc freq data=hav;                           | PERCENT  .   2019_4   100  22   22   22  166 */
    /*                        |  title"|AMT1|AMT2|AMT3|AMT4|SUM";              |                                              */
    /*                        |  tables ay_am*per/nopercent                    |                                              */
    /*                        |    norow nocol nocum;                          |                                              */
    /*                        |  weight amt;                                   |                                              */
    /*                        |  run;quit;                                     |                                              */
    /*                        |  %utl_odsfrq(outdsn=                           |                                              */
    /*                        |    want_frq);                                  |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        |  7 proc sql                                    | WORK.WANT                                    */
    /*                        |  ===========                                   |                                              */
    /*                        |                                                |   AY   AM AMT6 AMT7 AMT8 AMT9                */
    /*                        |  you can simplify with macro arrays            |                                              */
    /*                        |  you can also use partitioning to              |  2018   2   22  24    .    .                 */
    /*                        |  remove the hardcoding per                     |  2018   3   20  24   24    .                 */
    /*                        |                                                |  2018   4    .   .    .   84                 */
    /*                        |  proc sql;                                     |  2019   3   60  24    .    .                 */
    /*                        |    create                                      |  2019   4  100  22   22   22                 */
    /*                        |      table want as                             |                                              */
    /*                        |    select                                      |                                              */
    /*                        |      ay                                        |                                              */
    /*                        |     ,am                                        |                                              */
    /*                        |     ,sum(case when (per=6)                     |                                              */
    /*                        |         then amt else . end) as amt6           |                                              */
    /*                        |     ,sum(case when (per=7)                     |                                              */
    /*                        |         then amt else . end) as amt7           |                                              */
    /*                        |     ,sum(case when (per=8)                     |                                              */
    /*                        |         then amt else . end) as amt8           |                                              */
    /*                        |     ,sum(case when (per=9)                     |                                              */
    /*                        |         then amt else . end) as amt9           |                                              */
    /*                        |    from                                        |                                              */
    /*                        |      sd1.have                                  |                                              */
    /*                        |    group                                       |                                              */
    /*                        |      by ay, am                                 |                                              */
    /*                        |  ;quit;                                        |                                              */
    /*                        |                                                |                                              */
    /*                        |                                                |  WANT_ARY                                    */
    /*                        |  WITH MACRO ARRAYS                             |                                              */
    /*                        |                                                |   AY   AM AMT6 AMT7 AMT8 AMT9                */
    /*                        |  %array(_pr,values=6-9);                       |                                              */
    /*                        |                                                |  2018   2   22  24    .    .                 */
    /*                        |  proc sql;                                     |  2018   3   20  24   24    .                 */
    /*                        |    create                                      |  2018   4    .   .    .   84                 */
    /*                        |      table want_ary as                         |  2019   3   60  24    .    .                 */
    /*                        |    select                                      |  2019   4  100  22   22   22                 */
    /*                        |      ay                                        |                                              */
    /*                        |     ,am                                        |                                              */
    /*                        |     ,%do_over(_pr,phrase=%str(                 |                                              */
    /*                        |        sum(case when (per=?)                   |                                              */
    /*                        |         then amt else . end) as amt?)          |                                              */
    /*                        |        ,between=comma)                         |                                              */
    /*                        |    from                                        |                                              */
    /*                        |      sd1.have                                  |                                              */
    /*                        |    group                                       |                                              */
    /*                        |      by ay, am                                 |                                              */
    /*                        |  ;quit;                                        |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        |  8 R SQL                                       | SD1.WANT                                     */
    /*                        |  =======                                       |                                              */
    /*                        |                                                |  ROWNAMES  AY  AM AMT6 AMT7 AMT8 AMT9        */
    /*                        |  %utl_rbeginx;                                 |                                              */
    /*                        |  parmcards4;                                   |      1    2018  2   22  24    .    .         */
    /*                        |  library(haven)                                |      2    2018  3   20  24   24    .         */
    /*                        |  library(sqldf)                                |      3    2018  4    .   .    .   84         */
    /*                        |  source("c:/oto/fn_tosas9x.R")                 |      4    2019  3   60  24    .    .         */
    /*                        |  have<-read_sas("d:/sd1/have.sas7bdat")        |      5    2019  4  100  22   22   22         */
    /*                        |  print(have)                                   |                                              */
    /*                        |  want<-sqldf('                                 |                                              */
    /*                        |      select                                    |  > want                                      */
    /*                        |        ay                                      |      AY AM amt6 amt7 amt8 amt9               */
    /*                        |       ,am                                      |  1 2018  2   22   24   NA   NA               */
    /*                        |       ,sum(case when (per=6)                   |  2 2018  3   20   24   24   NA               */
    /*                        |           then amt else null end) as amt6      |  3 2018  4   NA   NA   NA   84               */
    /*                        |       ,sum(case when (per=7)                   |  4 2019  3   60   24   NA   NA               */
    /*                        |           then amt else null end) as amt7      |  5 2019  4  100   22   22   22               */
    /*                        |       ,sum(case when (per=8)                   |                                              */
    /*                        |           then amt else null end) as amt8      |                                              */
    /*                        |       ,sum(case when (per=9)                   |                                              */
    /*                        |           then amt else null end) as amt9      |                                              */
    /*                        |      from                                      |                                              */
    /*                        |        have                                    |                                              */
    /*                        |      group                                     |                                              */
    /*                        |        by ay, am                               |                                              */
    /*                        |  ')                                            |                                              */
    /*                        |  fn_tosas9x(                                   |                                              */
    /*                        |        inp    = want                           |                                              */
    /*                        |       ,outlib ="d:/sd1/"                       |                                              */
    /*                        |       ,outdsn ="want"                          |                                              */
    /*                        |       )                                        |                                              */
    /*                        |  ;;;;                                          |                                              */
    /*                        |  %utl_rendx;                                   |                                              */
    /*                        |                                                |                                              */
    /*                        |                                                |                                              */
    /*                        |  R arrays                                      |                                              */
    /*                        |  =========                                     |                                              */
    /*                        |                                                |                                              */
    /*                        |  %utl_rbeginx;                                 |                                              */
    /*                        |  parmcards4;                                   |                                              */
    /*                        |  library(haven)                                |                                              */
    /*                        |  library(sqldf)                                |                                              */
    /*                        |  source("c:/oto/fn_tosas9x.R")                 |                                              */
    /*                        |  have<-read_sas("d:/sd1/have.sas7bdat")        |                                              */
    /*                        |  amts<-c(6,7,8,9)                              |                                              */
    /*                        |  amts                                          |                                              */
    /*                        |  phrases <- paste(                             |                                              */
    /*                        |      sprintf(                                  |                                              */
    /*                        |      ",sum(case when (per= %1$s ) \n           |                                              */
    /*                        |        then amt else null end) \n              |                                              */
    /*                        |      as amt%2$s",amts,amts),collapse=' ')      |                                              */
    /*                        |  genqry<-paste("select ay, am"                 |                                              */
    /*                        |       ,phrases                                 |                                              */
    /*                        |       ,"from have group by ay, am")            |                                              */
    /*                        |  want<-sqldf(genqry)                           |                                              */
    /*                        |  want                                          |                                              */
    /*                        |  fn_tosas9x(                                   |                                              */
    /*                        |        inp    = want                           |                                              */
    /*                        |       ,outlib ="d:/sd1/"                       |                                              */
    /*                        |       ,outdsn ="want"                          |                                              */
    /*                        |       )                                        |                                              */
    /*                        |  ;;;;                                          |                                              */
    /*                        |  %utl_rendx;                                   |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        |  9 PYTHON SQL                                  | PYTHON                                       */
    /*                        |  ============                                  |                                              */
    /*                        |                                                |        AY   AM   amt6  amt7  amt8  amt9      */
    /*                        |  %utl_pybeginx;                                | 0  2018.0  2.0   22.0  24.0   NaN   NaN      */
    /*                        |  parmcards4;                                   | 1  2018.0  3.0   20.0  24.0  24.0   NaN      */
    /*                        |  exec(open('c:/oto/fn_python.py').read())      | 2  2018.0  4.0    NaN   NaN   NaN  84.0      */
    /*                        |  have,meta = \                                 | 3  2019.0  3.0   60.0  24.0   NaN   NaN      */
    /*                        |   ps.read_sas7bdat('d:/sd1/have.sas7bdat')     | 4  2019.0  4.0  100.0  22.0  22.0  22.0      */
    /*                        |  want=pdsql('''                          \     |                                              */
    /*                        |    select                                \     |                                              */
    /*                        |      ay                                  \     | SAS                                          */
    /*                        |     ,am                                  \     |                                              */
    /*                        |     ,sum(case when (per=6)               \     | SD1.PYWANT                                   */
    /*                        |         then amt else null end) as amt6  \     |                                              */
    /*                        |     ,sum(case when (per=7)               \     |   AY  AM AMT6  AMT7  AMT8  AMT9              */
    /*                        |         then amt else null end) as amt7  \     |                                              */
    /*                        |     ,sum(case when (per=8)               \     |  2018  2   22   24     .     .               */
    /*                        |         then amt else null end) as amt8  \     |  2018  3   20   24    24     .               */
    /*                        |     ,sum(case when (per=9)               \     |  2018  4    .    .     .    84               */
    /*                        |         then amt else null end) as amt9  \     |  2019  3   60   24     .     .               */
    /*                        |    from                                  \     |  2019  4  100   22    22    22               */
    /*                        |      have                                \     |                                              */
    /*                        |    group                                 \     |                                              */
    /*                        |      by ay, am                                 |                                              */
    /*                        |   ''');                                        |                                              */
    /*                        |  print(want);                                  |                                              */
    /*                        |  fn_tosas9x(want,outlib='d:/sd1/' \            |                                              */
    /*                        |   ,outdsn='pywant')                            |                                              */
    /*                        |  ;;;;                                          |                                              */
    /*                        |  %utl_pyendx;                                  |                                              */
    /*                        |                                                |                                              */
    /*                        |-----------------------------------------------------------------------------------------------*/
    /*                        |                                                |                                              */
    /*                        |  10 sas means and transpose                    | SD1.WANT                                     */
    /*                        |  ===========================                   |                                              */
    /*                        |                                                |  AY  AM _NAME_   _1  _2  _3  _4              */
    /*                        |  proc means data=have noprint nway;            |                                              */
    /*                        |  class ay am per;                              | 2018  2  AMT     22  24   .   .              */
    /*                        |  var amt;                                      | 2018  3  AMT     20  24  24   .              */
    /*                        |  output out=summary sum=;                      | 2018  4  AMT      .   .   .  84              */
    /*                        |  run;                                          | 2019  3  AMT     60  24   .   .              */
    /*                        |                                                | 2019  4  AMT    100  22  22  22              */
    /*                        |  proc transpose data=summary out=want;         |                                              */
    /*                        |  by ay am;                                     |                                              */
    /*                        |  id per;                                       |                                              */
    /*                        |  var amt;                                      |                                              */
    /*                        |  run;                                          |                                              */
    /*                        |                                                |                                              */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                         |                                              */
    /* 11 MORE COMPLEX TABULATE                                                |                                              */
    /* =======================                                                 |                                              */
    /*                                                                         |               H    H            W      W     */
    /*                                                                         |               Q    M            Q      M     */
    /* options FORMCHAR='|----|+|---+=|-/\<>*'; * use this;                    |          H    R    E     W      R      E     */
    /* %utl_odstab(setup);                                                     |          M    A    D     M      A      D     */
    /* proc tabulate data=sashelp.class;                                       |  A       E    N    I     E      N      I     */
    /*   title                                                                 |  G F M   A    G    A     A      G      A     */
    /*   "|AGE|FN|MN|HMean|hQRange|hMedian|wMean|wQRange|wMedian|";            |  E N N   N    E    N     N      E      N     */
    /*   class age sex;                                                        |                                              */
    /*   var height weight;                                                    |                                              */
    /*   tables age,                                                           |  1 1 1 54.40 6.20 54.4  67.75 34.50  67.75   */
    /*     (sex*n (height weight)*(mean qrange median))/rts=8;                 |  1 2 3 59.44 2.50 59.0  94.40 16.50  84.50   */
    /* run;quit;                                                               |  1 2 1 61.43 8.80 62.5  88.67 14.00  84.00   */
    /* %utl_odstab(outdsn=want_odsrpt,datarow=6);                              |  1 2 2 64.90 3.50 63.9 101.88 11.25 102.50   */
    /*                                                                         |  1 2 2 65.63 2.25 66.5 117.38 10.75 112.25   */
    /* * restore formchar;                                                     |  1 . 1 72.00 0.00 72.0 150.00  0.00 150.00   */
    /* options FORMCHAR='|----|+|---+=|-/\<>*';                                |                                              */
    /* run;quit;                                                               |                                              */
    /*                                                                         |                                              */
    /*                                                                         |                                              */
    /* |AGE|FN|MN|HMean|hQRange|hMedian|wMean|wQRange|wMedian|                 |                                              */
    /*                                                                         |                                              */
    /* -----------------------------------------------------------             |                                              */
    /* |   |    SEX   |                     |                    |             |                                              */
    /* |   |----------|                     |                    |             |                                              */
    /* |   |   F |  M |       HEIGHT        |       WEIGHT       |             |                                              */
    /* |   |-----+----+---------------------+--------------------|             |                                              */
    /* |   |   N |  M | Mean |QRange| Median| Mean |QRange|Median|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |AGE|     |    |      |      |       |      |      |      |             |                                              */
    /* |---|     |    |      |      |       |      |      |      |             |                                              */
    /* |11 | 1.00|1.00| 54.40|  6.20|  54.40| 67.75| 34.50| 67.75|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |12 | 2.00|3.00|  59.44   2.50|  59.00 94.40| 16.50| 84.50|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |13 | 2.00|1.00| 61.43|  8.80|  62.50| 88.67| 14.00| 84.00|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |14 | 2.00|2.00| 64.90|  3.50|  63.90|101.88| 11.25|102.50|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |15 | 2.00|2.00| 65.63|  2.25|  66.50|117.38| 10.75|112.25|             |                                              */
    /* |---+-----+----+------+------+-------+------+------+------|             |                                              */
    /* |16 |    .|1.00| 72.00|  0.00|  72.00|150.00|  0.00|150.00|             |                                              */
    /* -----------------------------------------------------------             |                                              */
    /*                                                                         |                                              */
    /*                                                                         |                                              */
    /* proc print data=want_odsrpt heading=vertical;                           |                                              */
    /* run;quit;                                                               |                                              */
    /*                                                                         |                                              */
    /**************************************************************************************************************************/


    /* ____             _       _           _
    / |___ \   _ __ ___| | __ _| |_ ___  __| |  _ __ ___ _ __   ___  ___
    | | __) | | `__/ _ \ |/ _` | __/ _ \/ _` | | `__/ _ \ `_ \ / _ \/ __|
    | |/ __/  | | |  __/ | (_| | ||  __/ (_| | | | |  __/ |_) | (_) \__ \
    |_|_____| |_|  \___|_|\__,_|\__\___|\__,_| |_|  \___| .__/ \___/|___/
                                                        |_|
    */


    REPO
    -----------------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl-adding-categories-that-are-NOT-in-your-data-to-proc-report-and--proc-tabulate
    https://github.com/rogerjdeangelis/utl-are-procedures-that-create-tables-better-than-proc-tabulate-output
    https://github.com/rogerjdeangelis/utl-avoiding-tabulate-in-order-to-get-a-sas-dataset-for-a-three-dimensional-crosstab
    https://github.com/rogerjdeangelis/utl-complex-proc-tabulate-in-proc-report-with-output-table-that-is-an-image-of-tabulate
    https://github.com/rogerjdeangelis/utl-conformal-ods-table-output-for-proc-tabulate-and-proc-freq
    https://github.com/rogerjdeangelis/utl-create-a-crosstab-dataset-instead-of-a-listing-from-proc-tabulate
    https://github.com/rogerjdeangelis/utl-create-useful-column-names-from-tabulate-output
    https://github.com/rogerjdeangelis/utl-creating-a-clinical-n-mean-stddev-median-min-max-sas-dataset-from-proc-tabulate
    https://github.com/rogerjdeangelis/utl-creating-a-new-variable-as-a-function-of-other-variables-using-just-proc-tabulate
    https://github.com/rogerjdeangelis/utl-creating-a-table-that-looks-like-the-proc-tabulate-listing-
    https://github.com/rogerjdeangelis/utl-creating-an-output-dataset-from-proc-tabulate-that-mimics-the-printed-data
    https://github.com/rogerjdeangelis/utl-crosstab-output-tables-from-corresp-report-not-static-tabulate
    https://github.com/rogerjdeangelis/utl-fixing-bugs-in-proc-report-tabulate-and-freq-output-crosstab-datasets
    https://github.com/rogerjdeangelis/utl-formatting-aggregated-across-cells-as-character-in-proc-report-not-possible-in-tabulate
    https://github.com/rogerjdeangelis/utl-how-to-get-a-sas-dataset-like-the-tabulate-listing
    https://github.com/rogerjdeangelis/utl-is-proc-report-more-flexible-than-proc-summary-or-tabulate-when-an-output-dataset-is-needed
    https://github.com/rogerjdeangelis/utl-make-proc-tabulate-dataset-output-more-usable
    https://github.com/rogerjdeangelis/utl-proc-tabulate-listing-converted-to-an-output-sas-dataset
    https://github.com/rogerjdeangelis/utl-proc-tabulate-using-a-second-dataset-to-capture-missing-columns-or-rows
    https://github.com/rogerjdeangelis/utl-reshape-table-use-utl_odsrpt-macro-and-proc-tabulate
    https://github.com/rogerjdeangelis/utl-simple-code-to-add-big-n-counts-to-proc-tabulate-static-output
    https://github.com/rogerjdeangelis/utl-simple-proc-report-and-tabulate-n-and-percent-crosstab-ourput-datasets-
    https://github.com/rogerjdeangelis/utl-three-dimensional-crosstab-proc-freq-tabulate-corresp-and-report
    https://github.com/rogerjdeangelis/utl-transpose-macro-rather-than-proc-print-report-or-tabulate
    https://github.com/rogerjdeangelis/utl-transposing-sorting-and-summarizing-with-a-single-proc-corresp-freq-tabulate-and-report
    https://github.com/rogerjdeangelis/utl-use-freq-and-corresp-table-output-to-avoid-tabulate-static-printouts
    https://github.com/rogerjdeangelis/utl-use-proc-report-instead-of-tabulate-most-of-the-time
    https://github.com/rogerjdeangelis/utl-use-report-instead-of-tabulate-and-get-the-bonus-of-an-output-table
    https://github.com/rogerjdeangelis/utl-using-excel-to-get-a-usefull-proc-tabulate-output-table
    https://github.com/rogerjdeangelis/utl_more_flexible_and_maintainable_crosstab_than_proc_tabulate
    https://github.com/rogerjdeangelis/utl_ods-output-datasets-for-a-very-complex-proc-tabulate
    https://github.com/rogerjdeangelis/utl_tabulate_or_report_spanning_lines
    https://github.com/rogerjdeangelis/utl_trying_to_do_too_much_with_proc_tabulate

    /* _____             _
    / |___ /    ___   __| |___   _ __ ___   __ _  ___ _ __ ___  ___
    | | |_ \   / _ \ / _` / __| | `_ ` _ \ / _` |/ __| `__/ _ \/ __|
    | |___) | | (_) | (_| \__ \ | | | | | | (_| | (__| | | (_) \__ \
    |_|____/   \___/ \__,_|___/ |_| |_| |_|\__,_|\___|_|  \___/|___/
           _   _              _     _        _
     _   _| |_| |    ___   __| |___| |_ __ _| |__
    | | | | __| |   / _ \ / _` / __| __/ _` | `_ \
    | |_| | |_| |  | (_) | (_| \__ \ || (_| | |_) |
     \__,_|\__|_|___\___/ \__,_|___/\__\__,_|_.__/
               |_____|
    */

    filename ft15f001 "c:/oto/utl_odstab.sas";
    parmcards4;
    %macro utl_odstab(outdsn,datarow=1);

       %if %qupcase(&outdsn)=SETUP %then %do;

            filename _tmp1_ clear;  * just in case;

            %utlfkil(%sysfunc(pathname(work))/_tmp1_.txt);

            filename _tmp1_ "%sysfunc(pathname(work))/_tmp1_.txt";

            %let _ps_= %sysfunc(getoption(ps));
            %let _fc_= %sysfunc(getoption(formchar));

            OPTIONS ls=max ps=32756  FORMCHAR='|'  nodate nocenter;

            title; footnote;

            proc printto print=_tmp1_;
            run;quit;

       %end;
       %else %do;

            /* %let outdsn=tst; %let datarow=3; */

            proc printto;
            run;quit;

            %utlfkil(%sysfunc(pathname(work))/_tmp2_.txt);

            *filename _tmp2_  "%sysfunc(pathname(work))/_tmp2_.txt";

            proc printto print="%sysfunc(pathname(work))/_tmp2_.txt";
            run;quit;

            data _null_;
              retain n 0;
              infile _tmp1_ length=l;
              input lyn $varying32756. l;
              if _n_=1 then do;
                  file print titles;
                  putlog lyn;
                  *put lyn;
              end;
              else do;
                 if countc(lyn,'|')>2;
                 n=n+1;
                 if n ge %eval(&datarow + 1) then do;
                    file print;
                    putlog lyn;
                    put lyn;
                 end;
              end;
            run;quit;

            proc printto;
            run;quit;

            proc import
               datafile="%sysfunc(pathname(work))/_tmp2_.txt"
               dbms=dlm
               out=&outdsn(drop=var:)
               replace;
               delimiter='|';
               getnames=yes;
            run;quit;

            filename _tmp1_ clear;
            filename _tmp2_ clear;

            %utlfkil(%sysfunc(pathname(work))/_tmp1_.txt);
            %utlfkil(%sysfunc(pathname(work))/_tmp2_.txt);

       %end;

    %mend utl_odstab;
    ;;;;
    run;quit;

    /*     _   _              _                _
     _   _| |_| |    ___   __| |___ _ __ _ __ | |_
    | | | | __| |   / _ \ / _` / __| `__| `_ \| __|
    | |_| | |_| |  | (_) | (_| \__ \ |  | |_) | |_
     \__,_|\__|_|___\___/ \__,_|___/_|  | .__/ \__|
               |_____|                  |_|
    */

    filename ft15f001 "c:/oto/utl_odsrpt.sas";
    parmcards4;
    %macro utl_odsrpt(outdsn);


       %if %qupcase(&outdsn)=SETUP %then %do;

            %put @@@@ &=sysindex.;

            %let _tmp1_=a&sysindex.;

            %put xxxx &=_tmp1_;

            filename &_tmp1_ clear;  * just in case;

            %utlfkil(%sysfunc(pathname(work))/&_tmp1_..txt);

            filename &_tmp1_ "%sysfunc(pathname(work))/&_tmp1_..txt";

            %let _ps_= %sysfunc(getoption(ps));
            %let _fc_= %sysfunc(getoption(formchar));

            OPTIONS ls=max ps=32756  FORMCHAR='|'  nodate nocenter;

            title; footnote;

            proc printto print=&_tmp1_;
            run;quit;

       %end;
       %else %do;

            /* %let outdsn=tst;  */
            %put @@@ &=sysindex.;

            %let _tmp2_=b&sysindex.;
            %let _tmp1_=a%eval(&sysindex - 2);

            %put xxxx  &=_tmp1_;
            %put xxxx  &=_tmp2_;

            proc printto;
            run;quit;

            filename &_tmp2_ clear;

            %utlfkil(%sysfunc(pathname(work))/&_tmp2_.txt);


            filename &_tmp2_ "%sysfunc(pathname(work))/&_tmp2_.txt";

            data _null_;
              infile &_tmp1_ length=l;
              input lyn $varying32756. l;
              if countc(lyn,'|')>1;
              lyn=compress(lyn);
              putlog lyn;
              file &_tmp2_;
              put lyn;
            run;quit;

            proc import
               datafile=&_tmp2_
               dbms=dlm
               out=&outdsn(drop=VAR:)
               replace;
               delimiter='|';
               getnames=yes;
            run;quit;

            filename &_tmp1_ clear;
            filename &_tmp2_ clear;

            %utlfkil(%sysfunc(pathname(work))/&_tmp1_.txt);
            %utlfkil(%sysfunc(pathname(work))/&_tmp2_.txt);

       %end;

    %mend utl_odsrpt;
    ;;;;
    run;quit;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
