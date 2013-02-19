Add I/O latency histograms to Tanel and Adrian's MOATS
see

* http://blog.tanelpoder.com/2011/03/29/moats-the-mother-of-all-tuning-scripts/
* http://www.oracle-developer.net/utilities.php

See also
 
* http://oracleprof.blogspot.ie/2012/07/average-active-session-in-sqlplus-with.html  AAS graph in SQL*Plus
* http://jagjeet.wordpress.com/2012/05/13/sqlplus-dashboard-for-rac/  SQL Dashboard for RAC
* http://jagjeet.wordpress.com/2012/09/20/sql-dashboard-v2/
* http://marcusmonnig.wordpress.com/2013/02/05/ash-data-viewer-in-mumbai-version-2-2-1-824/

Output looks like
    
    
    MOATS: The Mother Of All Tuning Scripts v1.0 by Adrian Billington & Tanel Poder
           http://www.oracle-developer.net & http://www.e2sn.com
    
    + INSTANCE SUMMARY ------------------------------------------------------------------------------------------+
    | Instance: V1               | Execs/s:  3050.1 | sParse/s:   205.5 | LIOs/s:   28164.9 | Read MB/s:    46.8 |
    | Cur Time: 18-Feb 12:08:22  | Calls/s:   633.1 | hParse/s:     9.1 | PhyRD/s:   5984.0 | Write MB/s:   12.2 |
    | History:  0h 0m 39s        | Commits/s: 446.7 | ccHits/s:  3284.6 | PhyWR/s:   1657.4 | Redo MB/s:     0.8 |
    +------------------------------------------------------------------------------------------------------------+
    |            event name avg ms   1ms   2ms   4ms   8ms  16ms  32ms  64ms 128ms 256ms 512ms    1s   2s+   4s+ |
    | db file scattered rea   .623     1                                                                         |
    | db file sequential re  1.413 13046  8995  2822   916   215     7                 1                         |
    |      direct path read  1.331    25    13     3           1                                                 |
    | direct path read temp  1.673                                                                               |
    |     direct path write  2.241    15    12    14     3                                                       |
    | direct path write tem  3.283                                                                               |
    | log file parallel wri                                                                                      |
    |         log file sync                                                                                      |
    
    + TOP SQL_ID (child#) -----+ TOP SESSIONS ---------+      + TOP WAITS -------------------------+ WAIT CLASS -+
    |  19% |  ()               |                       |      |  60% | db file sequential read     | User I/O    |
    |  19% | c13sma6rkr27c (0) | 245,147,374,386,267   |      |  17% | ON CPU                      | ON CPU      |
    |  17% | bymb3ujkr3ubk (0) | 131,10,252,138,248    |      |  15% | log file sync               | Commit      |
    |   9% | 8z3542ffmp562 (0) | 133,374,252,250       |      |   6% | log file parallel write     | System I/O  |
    |   9% | 0yas01u2p9ch4 (0) | 17,252,248,149        |      |   2% | read by other session       | User I/O    |
    +--------------------------------------------------+      +--------------------------------------------------+
    
    + TOP SQL_ID ----+ PLAN_HASH_VALUE + SQL TEXT ---------------------------------------------------------------+
    | c13sma6rkr27c  | 2583456710      | SELECT PRODUCTS.PRODUCT_ID, PRODUCT_NAME, PRODUCT_DESCRIPTION, CATEGORY |
    |                |                 | _ID, WEIGHT_CLASS, WARRANTY_PERIOD, SUPPLIER_ID, PRODUCT_STATUS, LIST_P |
    + ---------------------------------------------------------------------------------------------------------- +
    | bymb3ujkr3ubk  | 494735477       | INSERT INTO ORDERS(ORDER_ID, ORDER_DATE, CUSTOMER_ID, WAREHOUSE_ID) VAL |
    |                |                 | UES (ORDERS_SEQ.NEXTVAL + :B3 , SYSTIMESTAMP , :B2 , :B1 ) RETURNING OR |
    + ---------------------------------------------------------------------------------------------------------- +
    | 8z3542ffmp562  | 1655552467      | SELECT QUANTITY_ON_HAND FROM PRODUCT_INFORMATION P, INVENTORIES I WHERE |
    |                |                 |  I.PRODUCT_ID = :B2 AND I.PRODUCT_ID = P.PRODUCT_ID AND I.WAREHOUSE_ID  |
    + ---------------------------------------------------------------------------------------------------------- +
    | 0yas01u2p9ch4  | 0               | INSERT INTO ORDER_ITEMS(ORDER_ID, LINE_ITEM_ID, PRODUCT_ID, UNIT_PRICE, |
    |                |                 |  QUANTITY) VALUES (:B4 , :B3 , :B2 , :B1 , 1)                           |
    + ---------------------------------------------------------------------------------------------------------- +
    
    
