<a name="___top"></a>
# OpenWorm Repository Analysis

*   [Overview](#Overview)
*   [Instructions](#Instructions)
*   [Examples](#Examples)

<a name="Overview"></a>      []({{{1)
# [Overview &#9650;](#___top "click to go to top of document")
This repository provides tools for simple analysis of the OpenWorm codebase.  It is based on the excellent Perl script
[cloc](https://github.com/AlDanial/cloc).

### Repository Contents
* OW-repository-analysis: shell script which will scan the OpenWorm GitHub account, retrieve all of the repository names, collect data from each repository, and add them to the SQL database code.db
* openworm_repo_list.txt: pre-populated list of repositories.  This gets overwritten every time OW-repository-analysis is run
* code.db- SQLite database with information about all of the OpenWorm repositories.  This has already been pre-populated, and should be deleted when re-running the OW-repository-analysis script to update the database
* sqlite_formatter: formats output of SQL commands

<a name="Instructions"></a>      []({{{1)
# [Instructions &#9650;](#___top "click to go to top of document")

To simply work with the already populated database, no additional installation is necessary.  Simple download code.db and you can start your analysis.  

To re-populate the database with more recent data, first install cloc.  It can be installed by the standard package managers.  See the [cloc](https://github.com/AlDanial/cloc) repository for additional information:

    npm install -g cloc                    # https://www.npmjs.com/package/cloc
    sudo apt install cloc                  # Debian, Ubuntu
    sudo yum install cloc                  # Red Hat, Fedora
    sudo dnf install cloc                  # Fedora 22 or later
    sudo pacman -S cloc                    # Arch
    sudo emerge -av dev-util/cloc          # Gentoo https://packages.gentoo.org/packages/dev-util/cloc
    sudo apk add cloc                      # Alpine Linux
    sudo pkg install cloc                  # FreeBSD
    sudo port install cloc                 # Mac OS X with MacPorts
    brew install cloc                      # Mac OS X with Homebrew
    choco install cloc                     # Windows with Chocolatey
    scoop install cloc                     # Windows with Scoop

To begin the extraction process, simple run OW-repository analysis:

    ./OW-repository-Analysis  

The script OW-repo-analysis will do a shallow clone of every repository in order to run cloc.  These repositories are temporary and are automaticaly deleted at the end of each run.  There are over 60 OpenWorm repositories, so the process can take 5-10 minutes to complete.  You will be updated as each repository is cloned, analyzed, and the corresponding database entries are added:  

<pre>
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                               Dload  Upload   Total   Spent    Left  Speed
100  372k  100  372k    0     0   493k      0 --:--:-- --:--:-- --:--:--  493k
Analyzing behavioral_syntax . . .
Cloning into 'temp-behavioral_syntax'...
remote: Counting objects: 97, done.
remote: Compressing objects: 100% (88/88), done.
remote: Total 97 (delta 8), reused 57 (delta 3), pack-reused 0
Unpacking objects: 100% (97/97), done.
('temp-behavioral_syntax' will be deleted automatically)
1 repositories analyzed. 64 remaining.


Analyzing bionet . . .
Cloning into 'temp-bionet'...
remote: Counting objects: 206, done.
remote: Compressing objects: 100% (157/157), done.
remote: Total 206 (delta 58), reused 130 (delta 37), pack-reused 0
Receiving objects: 100% (206/206), 5.92 MiB | 5.31 MiB/s, done.
Resolving deltas: 100% (58/58), done.
('temp-bionet' will be deleted automatically)
2 repositories analyzed. 63 remaining.  
</pre>

<a name="Examples"></a>      []({{{1)
# [Examples &#9650;](#___top "click to go to top of document")
**What are the longest Python files over all projects?**

<pre>
prompt> sqlite3 code.db 'select project, file, nCode from t
                where language = "Python" order by nCode desc limit 20;' | ./sqlite_formatter

Project                         File                                                                                                     nCode
_______________________________ ________________________________________________________________________________________________________ _____
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/neuroml/nml/nml.py                  13829
org.geppetto.simulator.external temp-org.geppetto.simulator.external/src/test/resources/nsgConvertedModel/main_script.py                  4635
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/db/models/fields/related.py   1689
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/ec2/connection.py               1649
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/locale.py                                          1641
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/db/models/fields/__init__.py  1522
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/contrib/admin/options.py      1299
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/pip-1.1-py2.7.egg/pip/req.py         1241
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/db/models/sql/query.py        1182
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/test/_doctest.py              1166
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/rds2/layer1.py                  1118
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/db/models/query.py            1118
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/db/models/base.py             1012
c302                            temp-c302/c302/__init__.py                                                                                 994
CElegansNeuroML                 temp-CElegansNeuroML/CElegans/pythonScripts/c302/c302.py                                                   993
movement_cloud                  temp-movement_cloud/webworm/models.py                                                                      979
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/s3/bucket.py                     917
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/template/base.py               916
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/redshift/layer1.py               903
movement_cloud                  temp-movement_cloud/webworm/migrations/0002_db_250117.py
</pre>

**What are the most popular languages in each project?**

<pre>
prompt> sqlite3 code.db 'select project, language,sum(nCode) as SumCode from t
                group by project,language order by project,SumCode desc;' | ./sqlite_formatter

Project                              Language                   SumCode
____________________________________ __________________________ _______
CElegansNeuroML                      XML                         638108
CElegansNeuroML                      Python                        9411
CElegansNeuroML                      SQL                           6659
CElegansNeuroML                      XSLT                          2706
CElegansNeuroML                      Markdown                      2403
CElegansNeuroML                      HTML                          1942
CElegansNeuroML                      Bourne Shell                   258
CElegansNeuroML                      YAML                            74
CElegansNeuroML                      Dockerfile                      42
CElegansNeuroML                      DOS Batch                       16
CElegansNeuroML                      RobotFramework                   6
ChannelWorm                          HTML                         90850
ChannelWorm                          JavaScript                   75435
ChannelWorm                          CSS                          24950
ChannelWorm                          Python                        4376
ChannelWorm                          Markdown                      1879
ChannelWorm                          JSON                           353
ChannelWorm                          Sass                           114
ChannelWorm                          YAML                            62
ChannelWorm                          PHP                             20
ChannelWorm                          Bourne Again Shell              18
ChannelWorm                          SQL                             14
ChannelWorm                          Bourne Shell                     8
ChannelWorm                          INI                              4
CyberElegans                         C/C++ Header                  2490
CyberElegans                         C++                           2307
CyberElegans                         MSBuild script                 278
CyberElegans                         Windows Module Definition      120
CyberElegans                         YAML                            15
HeuristicWorm                        C++                           1346
HeuristicWorm                        C/C++ Header                   927
HeuristicWorm                        CMake                           10
HeuristicWorm                        Markdown                         5
JohnsonMailler_MuscleModel           Markdown                        44
JohnsonMailler_MuscleModel           HTML                            38
JohnsonMailler_MuscleModel           XML                             34
JohnsonMailler_MuscleModel           Bourne Shell                     8
OpenWorm                             Python                         154
OpenWorm                             Dockerfile                     116
OpenWorm                             Markdown                        76
OpenWorm                             Bourne Shell                    58
OpenWorm                             YAML                             5
PyOpenWorm                           XML                          18504
PyOpenWorm                           Python                        7886
PyOpenWorm                           Prolog                        4367
PyOpenWorm                           RobotFramework                1479
PyOpenWorm                           Markdown                       408
PyOpenWorm                           YAML                            35
PyOpenWorm                           Bourne Shell                    14
PyOpenWorm                           INI                             13
SegWorm                              HTML                         87981
SegWorm                              MATLAB                       55277
SegWorm                              PHP                            289
SegWorm                              CSS                             77
SegWorm                              Markdown                         9
WormWorx                             C                            11170
WormWorx                             C++                           3019
WormWorx                             XML                           2614
WormWorx                             C/C++ Header                  1830
WormWorx                             Java                          1734
WormWorx                             HTML                           249
WormWorx                             Bourne Again Shell             123
WormWorx                             CMake                           65
WormWorx                             DOS Batch                       64
WormWorx                             Groovy                          55
WormWorx                             YAML                            14
WormWorx                             JSON                             1
behavioral_syntax                    Python                        1474
behavioral_syntax                    DOS Batch                      227
behavioral_syntax                    make                           156
behavioral_syntax                    YAML                            33
behavioral_syntax                    Markdown                        11
bionet                               C++                          13726
bionet                               C/C++ Header                 13038
bionet                               Java                           438
bionet                               Markdown                       324
bionet                               make                           180
bionet                               Bourne Shell                   172
bionet                               XML                             58
bionet                               Bourne Again Shell              33
bionet                               DOS Batch                       16
c302                                 XML                          16827
c302                                 Python                        5707
c302                                 Markdown                      1979
c302                                 HTML                          1942
c302                                 Bourne Shell                    93
c302                                 YAML                            39
hodgkin_huxley_tutorial              XML                            160
hodgkin_huxley_tutorial              make                           143
hodgkin_huxley_tutorial              Python                         129
hodgkin_huxley_tutorial              JavaScript                      90
hodgkin_huxley_tutorial              YAML                            53
hodgkin_huxley_tutorial              Markdown                        15
hodgkin_huxley_tutorial              Bourne Shell                     3
hodgkin_huxley_tutorial              DOS Batch                        2
model_completion_dashboard           HTML                         87162
model_completion_dashboard           JavaScript                   66292
model_completion_dashboard           CSS                          26375
model_completion_dashboard           Markdown                      1640
model_completion_dashboard           Python                         935
model_completion_dashboard           JSON                           381
model_completion_dashboard           Sass                           114
model_completion_dashboard           PHP                             20
model_completion_dashboard           Bourne Again Shell              18
model_completion_dashboard           SQL                             14
model_completion_dashboard           Bourne Shell                    12
model_completion_dashboard           Dockerfile                       5
movement_cloud                       Python                        3043
movement_cloud                       JavaScript                    2367
movement_cloud                       SQL                           1162
movement_cloud                       HTML                           520
movement_cloud                       Markdown                       389
movement_cloud                       CSS                            373
movement_cloud                       Bourne Shell                    19
movement_validation_cloud            PO File                     206372
movement_validation_cloud            Python                      171221
movement_validation_cloud            JavaScript                    8673
movement_validation_cloud            HTML                          2745
movement_validation_cloud            CSS                           2218
movement_validation_cloud            JSON                           844
movement_validation_cloud            C                              178
movement_validation_cloud            XML                             68
movement_validation_cloud            Fish Shell                      50
movement_validation_cloud            Markdown                        40
movement_validation_cloud            C Shell                         19
movement_validation_cloud            YAML                            19
movement_validation_cloud            Bourne Shell                     7
muscle_model                         MATLAB                        1307
muscle_model                         Python                         939
muscle_model                         C++                            616
muscle_model                         C/C++ Header                   232
muscle_model                         Markdown                       222
muscle_model                         C                              205
muscle_model                         XML                            182
muscle_model                         YAML                            67
muscle_model                         HTML                            53
muscle_model                         Bourne Shell                    15
muscle_model                         make                            15
neuronal-analysis                    Python                         835
neuronal-analysis                    Markdown                        61
neuronal-analysis                    YAML                            18
neuronal-analysis                    Bourne Shell                     1
open-worm-analysis-toolbox           Python                        7727
open-worm-analysis-toolbox           Markdown                      2334
open-worm-analysis-toolbox           SQL                            440
open-worm-analysis-toolbox           YAML                            83
open-worm-analysis-toolbox           Bourne Shell                    48
openworm-scholar                     JSON                           985
openworm-scholar                     Python                         707
openworm-scholar                     INI                             93
openworm.github.io                   JavaScript                   14049
openworm.github.io                   HTML                         10049
openworm.github.io                   CSS                           9959
openworm.github.io                   YAML                            14
openworm.github.io                   Markdown                        12
openworm_docs                        Markdown                      1940
openworm_docs                        DOS Batch                      212
openworm_docs                        make                           143
openworm_docs                        YAML                            35
openworm_docs                        Python                          29
openworm_docs                        Bourne Shell                    19
openwormbrowser-ios                  HTML                         10506
openwormbrowser-ios                  Objective C                   9843
openwormbrowser-ios                  CSS                           6619
openwormbrowser-ios                  JSON                          6593
openwormbrowser-ios                  JavaScript                    1312
openwormbrowser-ios                  C/C++ Header                  1056
openwormbrowser-ios                  XML                            945
openwormbrowser-ios                  Markdown                       470
openwormbrowser-ios                  Bourne Shell                    23
openwormbrowser-ios                  YAML                            15
org.geppetto                         Python                         592
org.geppetto                         XML                            339
org.geppetto                         Bourne Shell                   304
org.geppetto                         Markdown                       239
org.geppetto                         Dockerfile                     226
org.geppetto                         JSON                           220
org.geppetto                         YAML                            31
org.geppetto                         Maven                           16
org.geppetto                         SQL                              4
org.geppetto.bower                   JavaScript                      75
org.geppetto.bower                   JSON                            53
org.geppetto.bower                   Markdown                         3
org.geppetto.core                    Java                          8954
org.geppetto.core                    JSON                          2142
org.geppetto.core                    XML                            490
org.geppetto.core                    Maven                          349
org.geppetto.core                    XMI                            291
org.geppetto.core                    JavaScript                      50
org.geppetto.core                    Velocity Template Language      12
org.geppetto.core                    YAML                            11
org.geppetto.core                    Markdown                         7
org.geppetto.datasources             Java                          1146
org.geppetto.datasources             Maven                          184
org.geppetto.datasources             XML                             95
org.geppetto.datasources             XMI                             31
org.geppetto.datasources             JSON                            18
org.geppetto.datasources             YAML                            14
org.geppetto.datasources             Velocity Template Language      12
org.geppetto.datasources             Markdown                         8
org.geppetto.docs                    HTML                        281123
org.geppetto.docs                    Markdown                      3049
org.geppetto.docs                    CSS                            533
org.geppetto.docs                    DOS Batch                      212
org.geppetto.docs                    make                           143
org.geppetto.docs                    Python                          51
org.geppetto.docs                    JavaScript                      29
org.geppetto.docs                    Bourne Shell                     7
org.geppetto.frontend                JavaScript                   48424
org.geppetto.frontend                LESS                          9255
org.geppetto.frontend                Java                          3965
org.geppetto.frontend                CSS                           2645
org.geppetto.frontend                Maven                          488
org.geppetto.frontend                JSON                           287
org.geppetto.frontend                XML                            136
org.geppetto.frontend                Handlebars                     127
org.geppetto.frontend                YAML                            80
org.geppetto.frontend                Bourne Shell                    68
org.geppetto.frontend                Markdown                        50
org.geppetto.frontend                HTML                            38
org.geppetto.frontend                Visualforce Component           14
org.geppetto.frontend                INI                             11
org.geppetto.frontend.jupyter        Python                         548
org.geppetto.frontend.jupyter        JavaScript                      80
org.geppetto.frontend.jupyter        CSS                             59
org.geppetto.frontend.jupyter        Markdown                        28
org.geppetto.frontend.nodejs         JavaScript                      28
org.geppetto.frontend.nodejs         JSON                            16
org.geppetto.frontend.nodejs         Markdown                         5
org.geppetto.maven                   Markdown                         3
org.geppetto.model                   Java                         18693
org.geppetto.model                   Maven                          225
org.geppetto.model                   XML                             89
org.geppetto.model                   XMI                             78
org.geppetto.model                   Markdown                         7
org.geppetto.model                   YAML                             4
org.geppetto.model.neuroml           Java                          5054
org.geppetto.model.neuroml           XMI                           2542
org.geppetto.model.neuroml           XML                            951
org.geppetto.model.neuroml           Maven                          226
org.geppetto.model.neuroml           Python                         177
org.geppetto.model.neuroml           JSON                           171
org.geppetto.model.neuroml           YAML                            15
org.geppetto.model.neuroml           Markdown                         8
org.geppetto.model.nwb               Java                           697
org.geppetto.model.nwb               Maven                          179
org.geppetto.model.nwb               XML                             95
org.geppetto.model.nwb               YAML                            17
org.geppetto.model.nwb               Markdown                         8
org.geppetto.model.swc               Java                           431
org.geppetto.model.swc               Maven                          174
org.geppetto.model.swc               XML                             95
org.geppetto.model.swc               YAML                            13
org.geppetto.model.swc               Markdown                         8
org.geppetto.persistence             Java                          2443
org.geppetto.persistence             Maven                          365
org.geppetto.persistence             XML                             83
org.geppetto.persistence             YAML                            82
org.geppetto.persistence             XMI                             66
org.geppetto.persistence             JavaScript                      16
org.geppetto.persistence             Markdown                         8
org.geppetto.recording               Unity-Prefab                783308
org.geppetto.recording               JavaScript                    1373
org.geppetto.recording               Python                        1226
org.geppetto.recording               HTML                          1126
org.geppetto.recording               CSS                            650
org.geppetto.recording               DOS Batch                      212
org.geppetto.recording               Patran Command Language        164
org.geppetto.recording               make                           143
org.geppetto.recording               XML                             92
org.geppetto.samples                 XML                         524763
org.geppetto.samples                 JavaScript                     207
org.geppetto.samples                 Python                          36
org.geppetto.sibernetic              XML                         651474
org.geppetto.sibernetic              Java                          1626
org.geppetto.sibernetic              Maven                          195
org.geppetto.sibernetic              XSD                            130
org.geppetto.sibernetic              Markdown                         8
org.geppetto.sibernetic              YAML                             6
org.geppetto.simulation              Java                          4020
org.geppetto.simulation              JSON                           456
org.geppetto.simulation              Maven                          187
org.geppetto.simulation              XML                            152
org.geppetto.simulation              XMI                            103
org.geppetto.simulation              JavaScript                      64
org.geppetto.simulation              YAML                            15
org.geppetto.simulation              Markdown                         8
org.geppetto.simulator.external      Python                        4895
org.geppetto.simulator.external      Java                          1649
org.geppetto.simulator.external      Maven                          200
org.geppetto.simulator.external      XML                            200
org.geppetto.simulator.external      YAML                            38
org.geppetto.simulator.external      Markdown                        15
org.geppetto.simulator.external      Bourne Shell                    14
org.geppetto.simulator.jlems         Java                           604
org.geppetto.simulator.jlems         Maven                          215
org.geppetto.simulator.jlems         XML                             56
org.geppetto.simulator.jlems         YAML                            10
org.geppetto.simulator.libroadrunner Maven                          175
org.geppetto.simulator.libroadrunner Java                           148
org.geppetto.simulator.libroadrunner XML                             54
org.geppetto.simulator.libroadrunner Markdown                         8
org.geppetto.simulator.libroadrunner YAML                             6
org.geppetto.simulator.sph           Maven                          135
org.geppetto.simulator.sph           Java                           102
org.geppetto.simulator.sph           XML                             49
org.geppetto.simulator.sph           Markdown                         7
org.geppetto.simulator.sph           YAML                             6
org.geppetto.solver.sph              XML                        1968328
org.geppetto.solver.sph              Java                          2828
org.geppetto.solver.sph              OpenCL                        1011
org.geppetto.solver.sph              Maven                          227
org.geppetto.solver.sph              YAML                            13
org.geppetto.solver.sph              Markdown                         7
org.geppetto.templatebundle          Maven                          178
org.geppetto.templatebundle          XML                             43
org.geppetto.templatebundle          Visualforce Component           10
org.geppetto.templatebundle          Java                             7
org.geppetto.templatebundle          Markdown                         4
org.geppetto.testbackend             Java                           578
org.geppetto.testbackend             JSON                           186
org.geppetto.testbackend             Maven                          167
org.geppetto.testbackend             YAML                            74
org.geppetto.testbackend             XML                             53
org.geppetto.testbackend             XMI                             40
org.geppetto.testbackend             JavaScript                      23
org.geppetto.testbackend             Markdown                         6
org.wormsim.bower                    JSON                            34
org.wormsim.bower                    Markdown                         3
org.wormsim.frontend                 JavaScript                   48989
org.wormsim.frontend                 LESS                          6232
org.wormsim.frontend                 XML                           1117
org.wormsim.frontend                 Java                           814
org.wormsim.frontend                 CSS                            798
org.wormsim.frontend                 Velocity Template Language     727
org.wormsim.frontend                 Maven                          483
org.wormsim.frontend                 JSON                           389
org.wormsim.frontend                 Python                          71
org.wormsim.frontend                 INI                             18
org.wormsim.frontend                 HTML                            17
org.wormsim.frontend                 Visualforce Component           16
org.wormsim.frontend                 YAML                             6
org.wormsim.frontend                 Markdown                         5
owcs                                 JavaScript                    9364
owcs                                 CSS                           2390
owcs                                 Python                         154
owcs                                 HTML                           126
owcs                                 Markdown                       116
pharyngeal_muscle_model              C                             1398
pharyngeal_muscle_model              Markdown                         4
pygeppetto                           XMI                         124006
pygeppetto                           Python                        1283
pygeppetto                           Markdown                        90
pygeppetto                           INI                             10
recurrent                            Python                         630
recurrent                            Markdown                        66
robots                               C                            57192
robots                               Bourne Shell                 28855
robots                               MATLAB                        5647
robots                               C/C++ Header                  5628
robots                               C++                           4818
robots                               m4                            2953
robots                               Fortran 77                    2794
robots                               Python                         688
robots                               Markdown                       196
robots                               make                            20
robots                               SWIG                            14
robots                               JSON                            11
robots                               YAML                             1
sibernetic                           C/C++ Header                 29333
sibernetic                           C++                           3369
sibernetic                           OpenCL                        3278
sibernetic                           Python                        1127
sibernetic                           Markdown                       605
sibernetic                           MSBuild script                 358
sibernetic                           JSON                           293
sibernetic                           Bourne Shell                    49
sibernetic                           make                            39
sibernetic                           YAML                            28
sibernetic_NEURON                    Python                        1008
sibernetic_NEURON                    Markdown                        13
sibernetic_config_gen                Python                         513
sibernetic_config_gen                Markdown                        57
sibernetic_config_gen                YAML                            27
simple-C-elegans                     XML                         564129
simple-C-elegans                     Python                         826
simple-C-elegans                     YAML                            19
simple-C-elegans                     Markdown                        15
simple-C-elegans                     RobotFramework                   6
simple-C-elegans                     Bourne Shell                     1
skeletonExtraction                   C/C++ Header               1413072
skeletonExtraction                   JavaScript                   83519
skeletonExtraction                   C++                          20809
skeletonExtraction                   HTML                          6103
skeletonExtraction                   OpenCL                         756
skeletonExtraction                   Markdown                       318
skeletonExtraction                   m4                             259
skeletonExtraction                   Perl                           144
skeletonExtraction                   Java                           135
skeletonExtraction                   CMake                           32
skeletonExtraction                   YAML                            20
skeletonExtraction                   DTD                             15
skeletonExtraction                   DOS Batch                       14
skeletonExtraction                   Bourne Shell                     4
tests                                Python                          91
tests                                Dockerfile                      60
tests                                Markdown                        42
tracker-commons                      Scala                         4180
tracker-commons                      MATLAB                        1846
tracker-commons                      Python                        1599
tracker-commons                      C++                           1400
tracker-commons                      Julia                         1222
tracker-commons                      Markdown                      1145
tracker-commons                      C                              430
tracker-commons                      SWIG                           192
tracker-commons                      JSON                           185
tracker-commons                      C/C++ Header                   110
tracker-commons                      make                            82
tracker-commons                      YAML                            71
tracker-commons                      Java                            43
tracker-commons                      R                                5
wormbrowser                          JavaScript                    5819
wormbrowser                          Python                         597
wormbrowser                          CSS                            174
wormbrowser                          HTML                           159
wormbrowser                          XML                             48
wormbrowser                          YAML                            15
wormbrowser                          Java                            10
wormbrowser                          JSON                             1
</pre>

**Which Python source files with more than 500 lines have a comment ratio below 5%?**

<pre>
prompt> sqlite3 code.db 'select project, file, nCode,nComment,(100.0*nComment)/(nComment+nCode) as comment_ratio from t
                where language="Python" and nCode > 500 and comment_ratio < 5 order by comment_ratio;' | ./sqlite_formatter

Project                   File                                                                                                nCode nComment comment_ratio     
_________________________ ___________________________________________________________________________________________________ _____ ________ _________________
movement_cloud            temp-movement_cloud/webworm/migrations/0002_db_250117.py                                              873        2 0.228571428571429
movement_validation_cloud temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/django/conf/locale/__init__.py   525        3 0.568181818181818
movement_cloud            temp-movement_cloud/webworm/models.py                                                                 979        7 0.709939148073022
movement_validation_cloud temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/beanstalk/response.py       540        7 1.27970749542962  
movement_validation_cloud temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/neuroml/nml/nml.py             13829      329 2.32377454442718  
movement_validation_cloud temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/mws/response.py             526       18 3.30882352941176  
CElegansNeuroML           temp-CElegansNeuroML/CElegans/pythonScripts/c302/tune/c302NetTuner.py                                 733       28 3.67936925098555
</pre>

**Which files in each project have the most code lines?**

<pre>
prompt> sqlite3 code.db 'select project,file,max(nCode) as nL from t
                group by project order by nL desc;' | ./sqlite_formatter

Project                              File                                                                                                              nL     
____________________________________ _________________________________________________________________________________________________________________ ______
org.geppetto.samples                 temp-org.geppetto.samples/SPH/LiquidElasticBig/sphModel_Elastic.xml                                               454855
org.geppetto.sibernetic              temp-org.geppetto.sibernetic/src/test/resources/sphModel_Elastic.xml                                              454855
org.geppetto.solver.sph              temp-org.geppetto.solver.sph/src/test/resources/sphModel_Elastic.xml                                              454855
pygeppetto                           temp-pygeppetto/tests/xmi-data/LargeConns.net.nml.xmi                                                              64214
CElegansNeuroML                      temp-CElegansNeuroML/CElegans/morphologies/PVDL.java.xml                                                           61647
simple-C-elegans                     temp-simple-C-elegans/notebooks/OpenWorm/morphologies/PVDL.java.xml                                                61647
robots                               temp-robots/WormSim/sundials-2.3.0/configure                                                                       22186
org.wormsim.frontend                 temp-org.wormsim.frontend/src/main/webapp/r.js                                                                     20279
org.geppetto.docs                    temp-org.geppetto.docs/source/_static/javadoc/index-all.html                                                       19849
skeletonExtraction                   temp-skeletonExtraction/ClientSide/examples-mihai/js/three.js                                                      18449
PyOpenWorm                           temp-PyOpenWorm/tests/test_data/PVDR.nml.rdf.xml                                                                   18059
movement_validation_cloud            temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/neuroml/nml/nml.py                            13829
sibernetic                           temp-sibernetic/inc/OpenCL/cl.hpp                                                                                   9958
bionet                               temp-bionet/CElegans/behavior/include/GLee.h                                                                        8521
model_completion_dashboard           temp-model_completion_dashboard/wsgi/static/admin/js/jquery.js                                                      6850
owcs                                 temp-owcs/sitestatic/admin/js/jquery.js                                                                             6850
ChannelWorm                          temp-ChannelWorm/channelworm/web_app/static/third_party/lte/plugins/jQueryUI/jquery-ui-1.10.3.js                    6716
openworm.github.io                   temp-openworm.github.io/js/jquery-3.2.1.js                                                                          6625
org.geppetto.frontend                temp-org.geppetto.frontend/src/main/webapp/js/pages/dashboard/js/libs/jquery/jqueryui/jquery-ui-1.8.3.min.js        6545
openwormbrowser-ios                  temp-openwormbrowser-ios/Pods/RegexKitLite/RegexKitLite.html                                                        5683
SegWorm                              temp-SegWorm/docs/SegWorm/SegWorm/Util/text2im.html                                                                 5431
org.geppetto.simulator.external      temp-org.geppetto.simulator.external/src/test/resources/nsgConvertedModel/main_script.py                            4635
org.geppetto.core                    temp-org.geppetto.core/src/test/java/org/geppetto/core/TestConvertBigDATToRecordingClass.java                       2536
org.geppetto.model.neuroml           temp-org.geppetto.model.neuroml/src/test/resources/c302_C0_Social.net.nml.xmi                                       2504
WormWorx                             temp-WormWorx/AndroidStudio/WormWorx++/.idea/workspace.xml                                                          2500
c302                                 temp-c302/examples/LEMS_c302_D1_Full.xml                                                                            1341
movement_cloud                       temp-movement_cloud/mrc_db_schema.sql                                                                               1162
openworm-scholar                     temp-openworm-scholar/arxiv.json                                                                                     985
CyberElegans                         temp-CyberElegans/prj/gl/GL.h                                                                                        975
org.geppetto.model                   temp-org.geppetto.model/src/main/java/org/geppetto/model/values/impl/ValuesPackageImpl.java                          811
wormbrowser                          temp-wormbrowser/org.openworm.wormbrowser.utils/org/openworm/wormbrowser/utils/Virtual_Worm.js                       749
tracker-commons                      temp-tracker-commons/src/scala/src/main/scala/Data.scala                                                             735
org.geppetto.recording               temp-org.geppetto.recording/docs/build/html/creators.html                                                            676
open-worm-analysis-toolbox           temp-open-worm-analysis-toolbox/open_worm_analysis_toolbox/features/posture_features.py                              660
org.geppetto.simulation              temp-org.geppetto.simulation/src/main/java/org/geppetto/simulation/manager/GeppettoManager.java                      649
pharyngeal_muscle_model              temp-pharyngeal_muscle_model/pm3 muscle + small current/CaL.c                                                        520
org.geppetto.testbackend             temp-org.geppetto.testbackend/src/test/java/org/geppetto/testbackend/test/neuroml/GeppettoManagerNeuroMLTest.java    465
org.geppetto.model.nwb               temp-org.geppetto.model.nwb/src/main/java/org/geppetto/model/nwb/ReadNWBFile.java                                    448
org.geppetto.persistence             temp-org.geppetto.persistence/src/main/java/org/geppetto/persistence/GeppettoDataManager.java                        378
HeuristicWorm                        temp-HeuristicWorm/src/WPopulation.hpp                                                                               370
org.geppetto.simulator.jlems         temp-org.geppetto.simulator.jlems/src/main/java/org/geppetto/simulator/jlems/JLEMSSimulatorService.java              346
recurrent                            temp-recurrent/src/recurrent/event_parser.py                                                                         326
org.geppetto                         temp-org.geppetto/eclipse/GeppettoFormatter.xml                                                                      291
sibernetic_config_gen                temp-sibernetic_config_gen/Generator/Generator.py                                                                    275
sibernetic_NEURON                    temp-sibernetic_NEURON/drawer/ogldrawer.py                                                                           268
openworm_docs                        temp-openworm_docs/docs/Community/repositories.md                                                                    250
org.geppetto.datasources             temp-org.geppetto.datasources/src/main/java/org/geppetto/datasources/ExecuteQueryVisitor.java                        250
behavioral_syntax                    temp-behavioral_syntax/docs/make.bat                                                                                 227
muscle_model                         temp-muscle_model/NeuroML2/C/testMuscleOpenworm.c                                                                    205
org.geppetto.frontend.jupyter        temp-org.geppetto.frontend.jupyter/src/jupyter_geppetto/geppetto_comm/GeppettoJupyterModelSync.py                    197
org.geppetto.templatebundle          temp-org.geppetto.templatebundle/pom.xml                                                                             178
org.geppetto.simulator.libroadrunner temp-org.geppetto.simulator.libroadrunner/pom.xml                                                                    175
org.geppetto.model.swc               temp-org.geppetto.model.swc/pom.xml                                                                                  174
OpenWorm                             temp-OpenWorm/master_openworm.py                                                                                     154
hodgkin_huxley_tutorial              temp-hodgkin_huxley_tutorial/Tutorial/Makefile                                                                       143
org.geppetto.simulator.sph           temp-org.geppetto.simulator.sph/pom.xml                                                                              135
neuronal-analysis                    temp-neuronal-analysis/laboratory/Space Embedding of Nematode Network.py                                             117
org.geppetto.bower                   temp-org.geppetto.bower/1.0/cameracontrols/CameraControls.js                                                          75
tests                                temp-tests/Dockerfile                                                                                                 60
JohnsonMailler_MuscleModel           temp-JohnsonMailler_MuscleModel/NeuroML2/channel_summary/ChannelInfo.html                                             38
org.wormsim.bower                    temp-org.wormsim.bower/bower.json                                                                                     34
org.geppetto.frontend.nodejs         temp-org.geppetto.frontend.nodejs/GEPPETTO.Backend.js                                                                 28
org.geppetto.maven                   temp-org.geppetto.maven/README.md                                                                                      3
</pre>

**What are the 10 files with the most comments?**

<pre>
prompt> sqlite3 code.db 'select project,file,max(nComment) as nL from t
                group by project order by nL desc limit 10;' | ./sqlite_formatter

Project                         File                                                                                                nL   
_______________________________ ___________________________________________________________________________________________________ ____
ChannelWorm                     temp-ChannelWorm/channelworm/web_app/static/third_party/lte/plugins/datatables/jquery.dataTables.js 6913
model_completion_dashboard      temp-model_completion_dashboard/wsgi/static/third_party/lte/plugins/datatables/jquery.dataTables.js 6913
org.wormsim.frontend            temp-org.wormsim.frontend/src/main/webapp/r.js                                                      4519
robots                          temp-robots/WormSim/sundials-2.3.0/configure                                                        3574
org.geppetto.model              temp-org.geppetto.model/src/main/java/org/geppetto/model/values/ValuesPackage.java                  3272
skeletonExtraction              temp-skeletonExtraction/ClientSide/examples-mihai/js/three.js                                       2351
movement_validation_cloud       temp-movement_validation_cloud/djangodev/lib/python2.7/site-packages/boto/ec2/connection.py         2058
owcs                            temp-owcs/sitestatic/admin/js/jquery.js                                                             1735
openworm.github.io              temp-openworm.github.io/js/jquery-3.2.1.js                                                          1696
org.geppetto.simulator.external temp-org.geppetto.simulator.external/src/test/resources/nsgConvertedModel/main_script.py            1565
</pre>
