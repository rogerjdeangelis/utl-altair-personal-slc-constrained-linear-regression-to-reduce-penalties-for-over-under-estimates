# utl-altair-personal-slc-constrained-linear-regression-to-reduce-penalties-for-over-under-estimates
Altair personal slc constrained linear regression to reduce penalties for over under estimates
    %let pgm=utl-altair-personal-slc-constrained-linear-regression-to-reduce-penalties-for-over-under-estimates;

    %stop_submission;

    Altair personal slc constrained linear regression to reduce penalties for over under estimates

    community.altair
    https://community.altair.com/discussion/57707
    https://community.altair.com/discussion/57707/provide-bias-to-regression-model-to-reduce-total-cost-of-mistakes?tab=all&utm_source=community-search&utm_medium=organic-search&utm_term=%20fifferential%20equations

    Currently I have a linear regression of weight on height and I need to compute a new linear model that
    minimizes my total penalty.

    if I underestimate values by 15% or more, I have to pay a $500
    if I overestimate values by 15% or more, I have to pay a $1000

    My model yeilds a $4,000 penalty.


    /********************************************************************************************************************/
    /*    CURRENT REGRESSION MODEL WITH PENALTIES        |        OPTIMIZED REGRESSION CONSTRAINED MODEL                */
    /*                                                   |                                                              */
    /*    WEIGHT = 42.29899 + 0.20296 * HEIGHT           |          WEIGHT =  43.04327 + 0.205773 * HEIGHT              */
    /*                                                   |                                                              */
    /* NAME    HEIGHT WEIGHT PRED_WGT      PCT PENALTY   |     NAME   HEIGHT WEIGHT PRED_WGT   PCT      PRED   PENALTY  */
    /*                                                   |                                                              */
    /* Joyce     50.5  51.3   52.5483   2.4333      .    |    Joyce     50.5  51.3   52.5497   4.1614  53.4348      .   */
    /* Louise    77.0  56.3   57.9267   2.8893      .    |    Louise    77.0  56.3   57.7865   4.5964  58.8878      .   */
    /* James     83.0  57.3   59.1444   3.2188      .    |    James     83.0  57.3   58.9722   4.9257  60.1224      .   */
    /* Alice     84.0  46.5   59.3474  27.6287   $500    |    Alice     84.0  46.5   59.1698  29.7380  60.3282   $500   */
    /* Jeffrey   84.0  77.5   59.3474 -23.4228   1000    |    Jeffrey   84.0  77.5   59.1698 -22.1572  60.3282  $1000   */
    /* Jane      84.5  59.8   59.4488  -0.5872      .    |    Jane      84.5  59.8   59.2686   1.0553  60.4311      .   */
    /* Thomas    85.0  57.5   59.5503   3.5658      .    |    Thomas    85.0  57.5   59.3674   5.2764  60.5340      .   */
    /* Judy      90.0  54.3   60.5651  11.5379      .    |    Judy      90.0  54.3   60.3555  13.3753  61.5628      .   */
    /* Barbara   98.0  65.3   62.1887  -4.7646      .    |    Barbara   98.0  65.3   61.9364  -3.2021  63.2090      .   */
    /* John      99.5  74.0   62.4932 -15.5498  $1000    |    John      99.5  74.0   62.2328 -14.1653  63.5177      .   */
    /* Carol    102.5  62.8   63.1021   0.4810      .    |    Carol    102.5  62.8   62.8257   2.1258  64.1350      .   */
    /* Henry    102.5  53.5   63.1021  17.9478   $500    |    Henry    102.5  53.5   62.8257  19.8785  64.1350   $500   */
    /* Mary     112.0  66.5   65.0301  -2.2103      .    |    Mary     112.0  66.5   64.7030  -0.6168  66.0898      .   */
    /* William  112.0  66.5   65.0301  -2.2103      .    |    William  112.0  66.5   64.7030  -0.6168  66.0898      .   */
    /* Janet    112.5  77.5   65.1316 -15.9592  $1000    |    Janet    112.5  77.5   64.8018 -14.5901  66.1927      .   */
    /* Alfred   112.5  59.0   65.1316  10.3926      .    |    Alfred   112.5  59.0   64.8018  12.1910  66.1927      .   */
    /* Robert   128.0  64.8   68.2774   5.3664      .    |    Robert   128.0  64.8   67.8649   7.0713  69.3822      .   */
    /* Ronald   133.0  67.0   69.2922   3.4212      .    |    Ronald   133.0  67.0   68.8529   5.0911  70.4110      .   */
    /* Philip   150.0  72.0   72.7425   1.0312      .    |    Philip   150.0  72.0   72.2124   2.6516  73.9092      .   */
    /*                                         ======    |                                                      =====   */
    /*                                         $4,000    |                                                     $2,000   */
    /********************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    /*--- current problem ---*/

    options validvarname=upcase;
    ods listing;
    data class;
     input name$ height weight;
    cards4;
    Joyce    50.5 51.3
    Louise   77.0 56.3
    James    83.0 57.3
    Alice    84.0 46.5
    Jeffrey  84.0 77.5
    Jane     84.5 59.8
    Thomas   85.0 57.5
    Judy     90.0 54.3
    Barbara  98.0 65.3
    John     99.5 74.0
    Carol   102.5 62.8
    Henry   102.5 53.5
    Mary    112.0 66.5
    William 112.0 66.5
    Janet   112.5 77.5
    Alfred  112.5 59.0
    Robert  128.0 64.8
    Ronald  133.0 67.0
    Philip  150.0 72.0
    ;;;;
    run;quit;

    options validvarname=upcase;
    proc reg data=class;
      model weight=height;
      output out=tst pred=pred_wgt;
    run;quit;

    data have;
      set tst;
      pct=100*(pred_wgt-weight)/weight;
      if pct le -15 then penalty=1000;
      if pct ge 15  then penalty=500;
    run;quit;

    proc print data=have width=min;
    run;quit;
    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

      NAME      HEIGHT    WEIGHT    PRED_WGT      PCT       PENALTY

     Joyce        50.5     51.3      52.5483      2.4333         .
     Louise       77.0     56.3      57.9267      2.8893         .
     James        83.0     57.3      59.1444      3.2188         .
     Alice        84.0     46.5      59.3474     27.6287       500
     Jeffrey      84.0     77.5      59.3474    -23.4228      1000
     Jane         84.5     59.8      59.4488     -0.5872         .
     Thomas       85.0     57.5      59.5503      3.5658         .
     Judy         90.0     54.3      60.5651     11.5379         .
     Barbara      98.0     65.3      62.1887     -4.7646         .
     John         99.5     74.0      62.4932    -15.5498      1000
     Carol       102.5     62.8      63.1021      0.4810         .
     Henry       102.5     53.5      63.1021     17.9478       500
     Mary        112.0     66.5      65.0301     -2.2103         .
     William     112.0     66.5      65.0301     -2.2103         .
     Janet       112.5     77.5      65.1316    -15.9592      1000
     Alfred      112.5     59.0      65.1316     10.3926         .
     Robert      128.0     64.8      68.2774      5.3664         .
     Ronald      133.0     67.0      69.2922      3.4212         .
     Philip      150.0     72.0      72.7425      1.0312         .

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    ooptions validvarname=upcase;
    ods listing;
    data class;
     input name$ height weight;
    cards4;
    Joyce    50.5 51.3
    Louise   77.0 56.3
    James    83.0 57.3
    Alice    84.0 46.5
    Jeffrey  84.0 77.5
    Jane     84.5 59.8
    Thomas   85.0 57.5
    Judy     90.0 54.3
    Barbara  98.0 65.3
    John     99.5 74.0
    Carol   102.5 62.8
    Henry   102.5 53.5
    Mary    112.0 66.5
    William 112.0 66.5
    Janet   112.5 77.5
    Alfred  112.5 59.0
    Robert  128.0 64.8
    Ronald  133.0 67.0
    Philip  150.0 72.0
    ;;;;
    run;quit;

    options validvarname=upcase;
    proc reg data=class;
      model weight=height;
      output out=tst pred=pred_wgt;
    run;quit;

    data have;
      set tst;
      pct=100*(pred_wgt-weight)/weight;
      if pct le -15 then penalty=1000;
      if pct ge 15  then penalty=500;
    run;quit;

    proc print data=have width=min;
    run;quit;

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    ods listing;
    proc datasets lib=work
      nodetails nolist;
       delete want chk;
    run;quit;

    proc r;
    export data=class r=have;
    submit;

    library(CVXR)

    print(have);
    actual <-  have$WEIGHT
    height <-  have$HEIGHT

    # Variables to optimize
    beta0 <- Variable(1)
    beta1 <- Variable(1)

    # Predicted weights using the linear model
    pred_wgt <- beta0 + beta1 * height

    # Relative error: (actual - predicted) / actual
    rel_error <- (actual - pred_wgt) / actual

    # Slack variables for penalties
    under_penalty <- Variable(length(actual), nonneg = TRUE)  # for underestimation > 15%
    over_penalty <- Variable(length(actual), nonneg = TRUE)   # for overestimation > 15%

    # Constraints for penalty activation
    constraints <- list(
      under_penalty >= 0,
      over_penalty >= 0,

      # under_penalty >= rel_error - (-0.15) if rel_error < -0.15 (means pred_wgt < 0.85 * actual)
      under_penalty >= (-0.15 - rel_error),

      # over_penalty >= rel_error - 0.15 if rel_error > 0.15 (means pred_wgt > 1.15 * actual)
      over_penalty >= (rel_error - 0.15)
    )

    # Objective: minimize squared error + penalties weighted by costs 500 and 1000
    objective <- sum_squares(actual - pred_wgt) + 500 * sum(under_penalty) + 1000 * sum(over_penalty)

    # Define problem
    problem <- Problem(Minimize(objective), constraints)

    # Solve problem
    result <- solve(problem)

    # Output solution
    cat("Optimized intercept (beta0):", result$getValue(beta0), "\n")
    cat("Optimized slope (beta1):", result$getValue(beta1), "\n")
    cat("Predicted weights:\n")
    want<-as.data.frame(t(c(intercept=result$getValue(beta0),slope=result$getValue(beta1))))
    want
    endsubmit;
    import data=want r=want;
    run;quit;

    proc print data=want;
    run;quit;

    proc sql;
      select
         intercept
        ,slope
      into
         :intercept trimmed
        ,:slope trimmed
      from
         want
    ;quit;

    %put &=intercept;
    %put &=slope;

    data chk;

     set class;
     pred=  &intercept +  &slope * height;
      pct=100*(pred-weight)/weight;
      if pct le -15 then penalty=1000;
      if pct ge 15  then penalty=500;

    run;quit;

    proc print data=chk width=min;
    run;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */


    WORK.WANT

    Obs    INTERCEPT       SLOPE

     1      43.0433      0.205772739


    WORK.CHK

       NAME      HEIGHT    WEIGHT    PRED_WGT      PCT        PRED      PENALTY

      Joyce        50.5     51.3      52.5497      4.1614    53.4348         .
      Louise       77.0     56.3      57.7865      4.5964    58.8878         .
      James        83.0     57.3      58.9722      4.9257    60.1224         .
      Alice        84.0     46.5      59.1698     29.7380    60.3282      $500
      Jeffrey      84.0     77.5      59.1698    -22.1572    60.3282     $1000
      Jane         84.5     59.8      59.2686      1.0553    60.4311         .
      Thomas       85.0     57.5      59.3674      5.2764    60.5340         .
      Judy         90.0     54.3      60.3555     13.3753    61.5628         .
      Barbara      98.0     65.3      61.9364     -3.2021    63.2090         .
      John         99.5     74.0      62.2328    -14.1653    63.5177         .
      Carol       102.5     62.8      62.8257      2.1258    64.1350         .
      Henry       102.5     53.5      62.8257     19.8785    64.1350      $500
      Mary        112.0     66.5      64.7030     -0.6168    66.0898         .
      William     112.0     66.5      64.7030     -0.6168    66.0898         .
      Janet       112.5     77.5      64.8018    -14.5901    66.1927         .
      Alfred      112.5     59.0      64.8018     12.1910    66.1927         .
      Robert      128.0     64.8      67.8649      7.0713    69.3822         .
      Ronald      133.0     67.0      68.8529      5.0911    70.4110         .
      Philip      150.0     72.0      72.2124      2.6516    73.9092         .
                                                                         =====
                                                                         $2000
    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    3314      proc r;
    NOTE: Using R version 4.5.1 (2025-06-13 ucrt) from d:\r451
    3315      export data=have r=penalty;
    NOTE: Creating R data frame 'penalty' from data set 'WORK.have'

    3316      submit;
    3317
    3318      library(CVXR)
    3319
    3320      actual <-  penalty$WEIGHT
    3321      height <-  penalty$HEIGHT
    3322
    3323      # Variables to optimize
    3324      beta0 <- Variable(1)
    3325      beta1 <- Variable(1)
    3326
    3327      # Predicted weights using the linear model
    3328      pred_wgt <- beta0 + beta1 * height
    3329
    3330      # Relative error: (actual - predicted) / actual
    3331      rel_error <- (actual - pred_wgt) / actual
    3332
    3333      # Slack variables for penalties
    3334      under_penalty <- Variable(length(actual), nonneg = TRUE)  # for underestimation > 15%
    3335      over_penalty <- Variable(length(actual), nonneg = TRUE)   # for overestimation > 15%
    3336
    3337      # Constraints for penalty activation
    3338      constraints <- list(
    3339        under_penalty >= 0,
    3340        over_penalty >= 0,
    3341
    3342        # under_penalty >= rel_error - (-0.15) if rel_error < -0.15 (means pred_wgt < 0.85 * actual)
    3343        under_penalty >= (-0.15 - rel_error),
    3344
    3345        # over_penalty >= rel_error - 0.15 if rel_error > 0.15 (means pred_wgt > 1.15 * actual)
    3346        over_penalty >= (rel_error - 0.15)
    3347      )
    3348
    3349      # Objective: minimize squared error + penalties weighted by costs 500 and 1000
    3350      objective <- sum_squares(actual - pred_wgt) + 500 * sum(under_penalty) + 1000 * sum(over_penalty)
    3351
    3352      # Define problem
    3353      problem <- Problem(Minimize(objective), constraints)
    3354
    3355      # Solve problem
    3356      result <- solve(problem)
    3357
    3358      # Output solution
    3359      cat("Optimized intercept (beta0):", result$getValue(beta0), "\n")
    3360      cat("Optimized slope (beta1):", result$getValue(beta1), "\n")
    3361      cat("Predicted weights:\n")
    3362      want<-as.data.frame(t(c(intercept=result$getValue(beta0),slope=result$getValue(beta1))))
    3363      want
    3364      endsubmit;

    NOTE: Submitting statements to R:

    >
    Attaching package: 'CVXR'
    The following object is masked from 'package:stats':
        power
    > library(CVXR)
    >
    > actual <-  penalty$WEIGHT
    > height <-  penalty$HEIGHT
    >
    > # Variables to optimize
    > beta0 <- Variable(1)
    > beta1 <- Variable(1)
    >
    > # Predicted weights using the linear model
    > pred_wgt <- beta0 + beta1 * height
    >
    > # Relative error: (actual - predicted) / actual
    > rel_error <- (actual - pred_wgt) / actual
    >
    > # Slack variables for penalties
    > under_penalty <- Variable(length(actual), nonneg = TRUE)  # for underestimation > 15%
    > over_penalty <- Variable(length(actual), nonneg = TRUE)   # for overestimation > 15%
    >
    > # Constraints for penalty activation
    > constraints <- list(
    +   under_penalty >= 0,
    +   over_penalty >= 0,
    +
    +   # under_penalty >= rel_error - (-0.15) if rel_error < -0.15 (means pred_wgt < 0.85 * actual)
    +   under_penalty >= (-0.15 - rel_error),
    +
    +   # over_penalty >= rel_error - 0.15 if rel_error > 0.15 (means pred_wgt > 1.15 * actual)
    +   over_penalty >= (rel_error - 0.15)
    + )
    >
    > # Objective: minimize squared error + penalties weighted by costs 500 and 1000
    > objective <- sum_squares(actual - pred_wgt) + 500 * sum(under_penalty) + 1000 * sum(over_penalty)
    >
    > # Define problem
    > problem <- Problem(Minimize(objective), constraints)
    >        bot
    > # Solve problem
    > result <- solve(problem)
    >
    > # Output solution
    > cat("Optimized intercept (beta0):", result$getValue(beta0), "\n")
    > cat("Optimized slope (beta1):", result$getValue(beta1), "\n")
    > cat("Predicted weights:\n")
    > want<-as.data.frame(t(c(intercept=result$getValue(beta0),slope=result$getValue(beta1))))

    NOTE: Processing of R statements complete

    > want
    3365      import data=want r=want;
    NOTE: Creating data set 'WORK.want' from R data frame 'want'
    NOTE: Column names modified during import of 'want'
    NOTE: Data set "WORK.want" has 1 observation(s) and 2 variable(s)

    3366      run;quit;
    NOTE: Procedure r step took :
          real time : 3.852
          cpu time  : 0.015


    3367
    3368      proc print data=want;
    3369      run;quit;
    NOTE: 1 observations were read from "WORK.want"
    NOTE: Procedure print step took :
          real time : 0.005
          cpu time  : 0.000


    3370
    3371      proc sql;
    3372        select
    3373           intercept
    3374          ,slope
    3375        into
    3376           :intercept trimmed
    3377          ,:slope trimmed
    3378        from
    3379           want
    3380      ;quit;
    NOTE: Procedure sql step took :
          real time : 0.030
          cpu time  : 0.000


    3381
    3382      %put &=intercept;
    intercept=43.043267733
    3383      %put &=slope;
    slope=0.205772739
    3384
    3385      data chk;
    3386
    3387       set have(drop=penalty);
    3388       pred=  &intercept +  &slope * height;
    3389        pct=100*(pred-weight)/weight;
    3390        if pct le -15 then penalty=1000;
    3391        if pct ge 15  then penalty=500;
    3392
    3393      run;

    NOTE: 19 observations were read from "WORK.have"
    NOTE: Data set "WORK.chk" has 19 observation(s) and 7 variable(s)
    NOTE: The data step took :
          real time : 0.012
          cpu time  : 0.015


    3393    !     quit;
    3394
    3395      proc print data=chk width=min;
    3396      run;quit;
    NOTE: 19 observations were read from "WORK.chk"
    NOTE: Procedure print step took :
          real time : 0.025
          cpu time  : 0.015


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
