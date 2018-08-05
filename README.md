# utl_ods_excel_conditionaly_higlight_individua1_cells
Ods excel conditionaly higlight individua1 cells.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Ods excel conditionaly higlight individua1 cells

    github
    https://tinyurl.com/y72jxuqn
    https://github.com/rogerjdeangelis/utl_ods_excel_conditionaly_higlight_individua1_cells

    SAS 9.2M2 Win 7 64bit ( posted code may work in later versions?)

    see SAS FORUM
    https://tinyurl.com/ya7bujbj
    https://communities.sas.com/t5/Base-SAS-Programming/CALL-DEFINE-Highlighting-cells-of-the-column/m-p/483559

     Key method - run proc report twice

    PROBLEM  ONLY 1,982 SHOULD HAVE A YELLOW BACKGROUND
    ===================================================

                                 Region                Product type
                       EAST                WEST   FURNITURE      OFFICE
     Country        PREDICT             PREDICT     PREDICT     PREDICT
     -----------------------        -----------------------------------

     CANADA        1,982<-YELLOW      912<-YELLOW   1,434        1,460
     GERMANY       1,810<-YELLOW    2,283<-YELLOW     686        3,407
     U.S.A.        1,387<-YELLOW    1,577             972        1,992

                 -------          -------          ------      -------
                   5,179            4,772           3,092        6,859

    SHOULD BE
    ---------
                            Region                Product type
                       EAST          WEST   FURNITURE      OFFICE
     Country        PREDICT       PREDICT     PREDICT     PREDICT
     -----------------------     --------------------------------

     CANADA        1,982<-YELLOW      912     1,434        1,460
     GERMANY       1,810            2,283       686        3,407
     U.S.A.        1,387            1,577       972        1,992

                 -------          -------    ------      -------
                   5,179            4,772     3,092        6,859

    INPUT
    =====

     WORK.HAVE total obs=19

      COUNTRY    REGION    PRODTYPE     PRODUCT    ACTUAL    PREDICT    MONTH

      CANADA      EAST     FURNITURE     BED         983       851      12205
      GERMANY     EAST     OFFICE        CHAIR       197       747      12113
      U.S.A.      EAST     OFFICE        DESK        415       763      12753
     .....
      CANADA      EAST     OFFICE        CHAIR       670       679      12113
      U.S.A.      EAST     FURNITURE     BED         153        37      12662
      GERMANY     WEST     FURNITURE     BED         468       576      12205


    PROCESS
    =======

    ods excel options(sheet_name="utl_100rpt" sheet_interval="none");
    ods escapechar='^';

    PROC REPORT DATA=WORK.HAVSEL LS=171 PS=65  SPLIT="/" HEADLINE HEADSKIP NOCENTER MISSING ;
    COLUMN  CB COUNTRY C2 C3 C4 C5;
    DEFINE  CB / DISPLAY noprint;
    DEFINE  COUNTRY / DISPLAY FORMAT= $CHAR10. WIDTH=10    SPACING=2   LEFT "Country" ;
    DEFINE  C2 / SUM "EAST" ;
    DEFINE  C3 / SUM "WEST" ;
    DEFINE  C4 / SUM "FURNITURE" ;
    DEFINE  C5 / SUM "OFFFICE" ;
    rbreak after / summarize skip ol;
    compute c2;
        if cb=0 then call define (_col_, "style", "style={background=yellow}");
    endcomp;
    RUN;QUIT;
    ods excel close;


    OUTPUT
    ======
                            Region                Product type
                       EAST          WEST   FURNITURE      OFFICE
     Country        PREDICT       PREDICT     PREDICT     PREDICT
     -----------------------     --------------------------------

     CANADA        1,982<-YELLOW      912     1,434        1,460
     GERMANY       1,810            2,283       686        3,407
     U.S.A.        1,387            1,577       972        1,992

                 -------          -------    ------      -------
                   5,179            4,772     3,092        6,859

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    PROC SQL;
    CREATE TABLE have AS
    SELECT   country
            ,region
            ,prodtype
            ,product
            ,actual
            ,predict
            ,month
    FROM sashelp.prdsale
    WHERE mod(monotonic(),75)=0
    ORDER BY ranuni(94612);
    QUIT;


    ods exclude all;
    ods output report=havRpt;
    proc report data=sale nowindows missing headline headskip;
        column country (region prodtype) , predict;
        define country / group;
        define region / across;
        define prodtype / across;
    run;quit;
    ods select all;
    ods excel close;

    data havSel(rename=(%utl_renamel(old=_c2_ _c3_ _c4_ _c5_,new=c2 c3 c4 c5)));
      retain cb;
      set havRpt(drop= _break_);
      if _c2_>_c3_ then cb=0;
      else cb=1;
      drop _break_;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    see process
