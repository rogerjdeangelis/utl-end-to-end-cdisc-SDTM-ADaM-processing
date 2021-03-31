# utl-end-to-end-cdisc-SDTM-ADaM-processing
Create define.xml, SDTM, and ADaM expors for FDA submission
 
    This code should run 'lights out', day or night. All data is downloaded from GitHub
    programatically, except the makefile.
    
    Don not extract code from this readme, use cln_000makefile.sas instead.
    
    Occasionally a download might fail. When this happens check you log and just rerun the download code.
    Also you can download tne b64 coded file manually.
 
    Download the makefile into the Classic 1980s SAS editor and highlight and submit sections of code.
    You should be able to run the entire script but I suggest you do a piece at a time.
    I have seen isues when I try to run 30 back to back programatic downloads.
 
    After setup you could put together a short driver that just calls the macros.
    But I think that is a little premature. Code is under development.
 
    data;infile"./&pgm..sas";file".j.sas";input;_infile_=trim(_infile_);put _infile_;run;
 
    *****************************************************************************************************************;
    *                                                                                                               *;
    *; %let pgm=cln_000makefile;                                                                                    *;
    *                                                                                                               *;
    *; %let purpose=An example of CDISC end to end processing of a clinical traial;                                 *;
    *                                                                                                               *;
    *; %put &=pgm;                                                                                                  *;
    *; %put &=purpose;                                                                                              *;
    *                                                                                                               *;
    *  I suggest you run sections of this code by highlighting and submitting.                                      *;
    *  The entire script should run but I am not sure about all the dependecies.                                    *;
    *                                                                                                               *;
    *  This code s still under development.                                                                         *;
    *                                                                                                               *;
    * ==============================================================================================================*;
    *                                                                                                               *;
    *  PROJECT TOKEN = cln                                                                                          *;
    *                                                                                                               *;
    *  WIN 10 64bit SAS 9.4M6(64bit)  (This code will not run in lockdown an probaly not in EG.                     *;
    *  Best with Classic SAS                                                                                        *;
    *                                                                                                               *;
    *  Documentation                                                                                                *;
    *  ==============                                                                                               *;
    *                                                                                                               *;
    *  Origin (I have heavily modified this repository- for better or worse)                                        *;
    *  https://github.com/wyp1125/SAS-Clinical-Trials-Toolkit                                                       *;
    *  Many thanks to the author.                                                                                   *;
    *                                                                                                               *;
    *  This is by no means a full and perfect clinical trial.                                                       *;
    *  Not all codelists, domains and values are present.                                                           *;
    *  This is just an example code. Maybe it can be educational.                                                   *;
    *                                                                                                               *;
    *  All input data ia in my github repository                                                                    *;
    *  https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing                  *;
    *             _                        _   _                   _                                                *;
    *    _____  _| |_ ___ _ __ _ __   __ _| | (_)_ __  _ __  _   _| |_ ___                                          *;
    *   / _ \ \/ / __/ _ \ `__| `_ \ / _` | | | | `_ \| `_ \| | | | __/ __|                                         *;
    *  |  __/>  <| ||  __/ |  | | | | (_| | | | | | | | |_) | |_| | |_\__ \                                         *;
    *   \___/_/\_\\__\___|_|  |_| |_|\__,_|_| |_|_| |_| .__/ \__,_|\__|___/                                         *;
    *                                                 |_|                                                           *;
    *                                                                                                               *;
    *  KEY EXTERNAL INPUT SAS EXCEL WORKBOOKS WITH DETAIL META DATA                                                 *;
    *  THES WORKBOOKS DRIVE ALL THE ANALYSIS EVEN THE CREATION OF THE DEFINE XML                                    *;
    *  =========================================================================                                    *;
    *                     _                  _              _       _                                               *;
    *    _____  _____ ___| |  _ __ ___   ___| |_ __ _    __| | __ _| |_ __ _                                        *;
    *   / _ \ \/ / __/ _ \ | | `_ ` _ \ / _ \ __/ _` |  / _` |/ _` | __/ _` |                                       *;
    *  |  __/>  < (_|  __/ | | | | | | |  __/ || (_| | | (_| | (_| | || (_| |                                       *;
    *   \___/_/\_\___\___|_| |_| |_| |_|\___|\__\__,_|  \__,_|\__,_|\__\__,_|                                       *;
    *                                                                                                               *;
    *  GitHub  (Workbooks are downloaded programatically)                                                           *;
    *                                                                                                               *;
    *  cln_sdtmMetadata.xlsx     SDTM                                                                               *;
    *  ======================                                                                                       *;
    *                                                                                                               *;
    *    https://tinyurl.com/y2daeq4v                                                                               *;
    *                                                                                                               *;
    *                                                                                                               *;
    *  cln_adamMetadata.xlsx     ADaM                                                                               *;
    *  ======================                                                                                       *;
    *                                                                                                               *;
    *    https://tinyurl.com/y4kpeosh                                                                               *;
    *                                                                                                               *;
    *                                                                                                               *;
    *                                                                                                               *;
    *  KEY EXTERNAL INPUT                                                                                           *;
    *  ==================                                                                                           *;
    *                                                                                                               *;
    *  GROUND TRUTH META DATA(Meta data driven processing)                                                          *;
    *  Below are the key sheets and tabs to produce ADAM and SDTM SAS datasets                                      *;
    *  We start with two excel workbooks then create the define.xml from the workbooks.                             *;
    *                                                                                                               *;
    *  We use th meta data in the excel workbooks, not the define XML.                                              *;
    *  The define XML is just excel output directed to ODM xlm sections. The ODM sections are                       *;
    *  concatenated to produce the SDTM an ADaM define XML files.                                                   *;
    *            _ _                              _                                                                 *;
    *    ___  __| | |_ _ __ ___    _ __ ___   ___| |_ __ _                                                          *;
    *   / __|/ _` | __| `_ ` _ \  | `_ ` _ \ / _ \ __/ _` |                                                         *;
    *   \__ \ (_| | |_| | | | | | | | | | | |  __/ || (_| |                                                         *;
    *   |___/\__,_|\__|_| |_| |_| |_| |_| |_|\___|\__\__,_|                                                         *;
    *                                                                                                               *;
    *   All the input data is programatically downloaded from GiTHub                                                *;
    *                                                                                                               *;
    *    &gbl_root/cln/xls/cln_sdtmMetadata.xlsx  (excel image of sdtm_define.xml -download from Github )           *;
    *                                                                                                               *;
    *      SHEETS                                                                                                   *;
    *                                Member                                                                         *;
    *       Name                     Type   Vars                                                                    *;
    *                                                                                                               *;
    *       DEFINE_HEADER_METADATA$  DATA     9                                                                     *;
    *       TOC_METADATA$            DATA    10                                                                     *;
    *       VARIABLE_METADATA$       DATA    16                                                                     *;
    *       VALUELEVEL_METADATA$     DATA    15                                                                     *;
    *       COMPUTATION_METHOD$      DATA     2                                                                     *;
    *       CODELISTS$               DATA    11                                                                     *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET DEFINE_HEADER_METADATA                                                                               *;
    *    ============================                                                                               *;
    *                                                                                                               *;
    *       Middle Observation(1 ) of DEFINE_HEADER_METADATA - Total Obs 1                                          *;
    *                                                                                                               *;
    *       Variable         Type/Length   Sample Value                                                             *;
    *                                                                                                               *;
    *       STUDYOID              N8       123                                                                      *;
    *                                                                                                               *;
    *       FILEOID               C6       XYZ123                                                                   *;
    *       STUDYNAME             C6       XYZ123                                                                   *;
    *       STUDYDESCRIPTION      C132     A PHASE IIB, DOU                                                         *;
    *       PROTOCOLNAME          C6       XYZ123                                                                   *;
    *       STANDARD              C4       SDTM                                                                     *;
    *       VERSION               C5       3.1.2                                                                    *;
    *       SCHEMALOCATION        C48      http://www.cdisc                                                         *;
    *       STYLESHEET            C15      define1-0-0.xsl                                                          *;
    *       CREATIONDATE          C19      2021-01-23T11:48                                                         *;
    *       TOTOBS                C16      1                                                                        *;
    *                                                                                                               *;
    *    SHEET TOC_METADATA                                                                                         *;
    *    ==================                                                                                         *;
    *                                                                                                               *;
    *      Middle Observation(5 ) of TOC_METADATA - Total Obs 11                                                    *;
    *                                                                                                               *;
    *      Variable         Type/Length   Middle Value                                                              *;
    *                                                                                                               *;
    *      DOMAIN                C6       SUPPDM                                                                    *;
    *      NAME                  C6       SUPPDM                                                                    *;
    *      REPEATING             C3       Yes                                                                       *;
    *      ISREFERENCEDATA       C3       No                                                                        *;
    *      PURPOSE               C12      Tabulation                                                                *;
    *      LABEL                 C34      DM - Supplementa                                                          *;
    *      STRUCTURE             C51      Supplemental Qua                                                          *;
    *      DOMAINKEYS            C48      STUDYID, RDOMAIN                                                          *;
    *      CLASS                 C22      Supplemental Qua                                                          *;
    *      ARCHIVELOCATIONID     C8       ./suppdm                                                                  *;
    *      TOTOBS                C16      11                                                                        *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET VARIABLE_METADATA                                                                                    *;
    *    =======================                                                                                    *;
    *                                                                                                               *;
    *      Middle Observation(64 ) of VARIABLE_METADATA - Total Obs 128                                             *;
    *                                                                                                               *;
    *       Variable         Type/Length   Sample Value                                                             *;
    *                                                                                                               *;
    *      DOMAIN                C6       LB              ** DOMAIN (eleven domains)                                *;
    *      VARIABLE              C8       LBDTC                                                                     *;
    *      VARNUM                N8       21                                                                        *;
    *      LENGTH                N8       16                                                                        *;
    *      TYPE                  C7       date                                                                      *;
    *      LABEL                 C40      Date/Time of Spe                                                          *;
    *      SIGNIFICANTDIGITS     C1                                                                                 *;
    *      ORIGIN                C18      Central laborato                                                          *;
    *      DISPLAYFORMAT         C3                                                                                 *;
    *      COMPUTATIONMETHODOID  C14                                                                                *;
    *      CODELISTNAME          C8                                                                                 *;
    *      MANDATORY             C3       No                                                                        *;
    *      ROLE                  C18      Timing                                                                    *;
    *      ROLECODELIST          C8       ROLECODE                                                                  *;
    *      VALUELISTOID          C13                                                                                *;
    *      ITEMOID               C40      LB.LBDTC                                                                  *;
    *      TOTOBS                C16      128                                                                       *;
    *      COMMENT               C2000                                                                              *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET VALUELEVEL_METADATA                                                                                  *;
    *    =========================                                                                                  *;
    *                                                                                                               *;
    *      Middle Observation(5 ) of VALUELEVEL_METADATA - Total Obs 10                                             *;
    *                                                                                                               *;
    *      Variable         Type/Length   Sample Value                                                              *;
    *                                                                                                               *;
    *      VALUELISTOID          C13      LBTESTCDVALUE                                                             *;
    *      VALUENAME             C6       BILDIR         * LAB TEST                                                 *;
    *      TYPE                  C5       float                                                                     *;
    *      LENGTH                N8       8                                                                         *;
    *      LABEL                 C32      Direct Bilirubin                                                          *;
    *      SIGNIFICANTDIGITS     C1       1                                                                         *;
    *      ORIGIN                C18      Central laborato                                                          *;
    *      DISPLAYFORMAT         C3       4.2                                                                       *;
    *      COMPUTATIONMETHODOID  C1                                                                                 *;
    *      CODELISTNAME          C1                                                                                 *;
    *      MANDATORY             C2       No                                                                        *;
    *      ROLE                  C1                                                                                 *;
    *      ROLECODELIST          C1                                                                                 *;
    *      ITEMOID               C200     LBTESTCDVALUE.BI                                                          *;
    *      TOTOBS                C16      10                                                                        *;
    *      COMMENT               C2000                                                                              *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET COMPUTATION_METHOD                                                                                   *;
    *    =========================                                                                                  *;
    *                                                                                                               *;
    *     Middle Observation(1 ) of COMPMETHOD - Total Obs 1                                                        *;
    *                                                                                                               *;
    *      Variable         Type/Length   Sample Value                                                              *;
    *                                                                                                               *;
    *     COMPUTATIONMETHODOID   C14      AGECALCULATION                                                            *;
    *     COMPUTATIONMETHOD      C42      integer value of (BRTHDT - RFSTDTC)/365.25                                *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET CODELISTS                                                                                            *;
    *    ===============                                                                                            *;
    *                                                                                                               *;
    *     Middle Observation(67 ) of CODELISTS - Total Obs 135                                                      *;
    *                                                                                                               *;
    *       Variable         Type/Length   Sample Value                                                             *;
    *                                                                                                               *;
    *     CODELISTNAME           C8       SEX                                                                       *;
    *     CODEDVALUE             C36      F                                                                         *;
    *     RANK                   N 8      3                                                                         *;
    *     TRANSLATED             C46      FEMALE                                                                    *;
    *     TYPE                   C4       TEXT                                                                      *;
    *     CODELISTDICTIONARY     C6                                                                                 *;
    *     CODELISTVERSION        C3                                                                                 *;
    *     SOURCEDATASET          C11      demographic                                                               *;
    *     SOURCEVARIABLE         C8       gender                                                                    *;
    *     SOURCEVALUE            C11      .                                                                         *;
    *     SOURCETYPE             C9       number                                                                    *;
    *     TOTOBS                 C16      135                                                                       *;
    *                                                                                                               *;
    *              _                                  _                                                             *;
    *     __ _  __| | __ _ _ __ ___    _ __ ___   ___| |_ __ _                                                      *;
    *    / _` |/ _` |/ _` | `_ ` _ \  | `_ ` _ \ / _ \ __/ _` |                                                     *;
    *   | (_| | (_| | (_| | | | | | | | | | | | |  __/ || (_| |                                                     *;
    *    \__,_|\__,_|\__,_|_| |_| |_| |_| |_| |_|\___|\__\__,_|                                                     *;
    *                                                                                                               *;
    *    ADAM has the sheets above and two additional sheets (excel image of adam_define.xml-download from Github ) *;
    *                                                                                                               *;
    *    &gbl_root/cln/xls/cln_adamMetadata.xlsx                                                                    *;
    *                                                                                                               *;
    *                                                                                                               *;
    *    SHEET ANALYSIS_RESULTS                                                                                     *;
    *    ======================                                                                                     *;
    *                                                                                                               *;
    *    Middle Observation(2 ) of ANALYSISRESULTS - Total Obs 5                                                    *;
    *                                                                                                               *;
    *      Variable        Type/Length   Sample Value                                                               *;
    *                                                                                                               *;
    *     ANALYSISDATASET        C5       ADEF                                                                      *;
    *     DISPLAYID              C13      Table_14.2.1                                                              *;
    *     DISPLAYNAME            C47      Responder Analysis by Visit                                               *;
    /*    PROGRAMMINGCODE        C800     procfreq;data=adef;bycrit1avisitn;tablestrtpn*crit1fl;run                */;
    *     DOCLEAFID              C40      SAP_Section_9.2.                                                          *;
    *     RESULTID               C43      Responder Analysis by Visit                                               *;
    *     REASON                 C58      Efficacy endpoint pre-specified in SAP                                    *;
    *     PARAMLIST              C41      XPPAIN/Pain Scor                                                          *;
    *     ANALYSISVARIABLES      C22      CRIT1FL                                                                   *;
    *     SELECTIONCRITERIA      C54      Where ITTFL='Y' and ABLFL ^= 'Y' and PARAMCD='XPPAIN'                     *;
    *     DOCUMENTATION          C24      SAP_Section_9.2.                                                          *;
    *     PROGRAM                                                                                                   *;
    *                                                                                                               *;
    *    SHEET EXTERNALLINKS                                                                                        *;
    *    ===================                                                                                        *;
    *                                                                                                               *;
    *     Middle Observation(3 ) of externallinks - Total Obs 6                                                     *;
    *                                                                                                               *;
    *     LEAFID                 C17      SAP_Section_9.2.                                                          *;
    *     LEAFRELPATH            C130     .\studyxyz123\studyxyz123_csr.pdf#nameddest=SAP_SEC_9.2.2                 *;
    *     TITLE                  C17      SAP Section 9.2.                                                          *;
    *                                                                                                               *;
    *                         ____ ____  _____       _       _                                                      *;
    *   _ __ __ ___      __  / ___|  _ \|  ___|   __| | __ _| |_ __ _                                               *;
    *  | `__/ _` \ \ /\ / / | |   | |_) | |_     / _` |/ _` | __/ _` |                                              *;
    *  | | | (_| |\ V  V /  | |___|  _ <|  _|   | (_| | (_| | || (_| |                                              *;
    *  |_|  \__,_| \_/\_/    \____|_| \_\_|      \__,_|\__,_|\__\__,_|                                              *;
    *                                                                                                               *;
    *                                                                                                               *;
    *  RAW CRF SOURCE SAS DATASETS (DOWNLOAD)                                                                       *;
    *  ---------------------------------------                                                                      *;
    *                                                                                                               *;
    *       Base64 encoded SAS raw for programatic downloads                                                        *;
    *       Downloading binary data is problematic so I provide                                                     *;
    *       encoded text files and SAS tools to covert to decode into binary SAS datasets.                          *;
    *                                                                                                               *;
    *         CLN_RAWADVERSE.B64                                                                                    *;
    *         CLN_RAWDEMOGRAPHIC.B64                                                                                *;
    *         CLN_RAWDOSING.B64                                                                                     *;
    *         CLN_RAWLABS.B64                                                                                       *;
    *         CLN_RAWPAIN.B64                                                                                       *;
    *                                                                                                               *;
    *       I aslo provide SAS datasets however I do use these, You can download and eliminate the b64 decode/      *;
    *                                                                                                               *;
    *       SAS V9 datasets                                                                                         *;
    *         CLN_RAWADVERSE.SAS7BDAT                                                                               *;
    *         CLN_RAWDEMOGRAPHIC.SAS7BDAT                                                                           *;
    *         CLN_RAWDOSING.SAS7BDAT                                                                                *;
    *         CLN_RAWLABS.SAS7BDAT                                                                                  *;
    *         CLN_RAWPAIN.SAS7BDAT                                                                                  *;
    *                                                                                                               *;
    *      ___    _    ____    _              _                                                                     *;
    *    / ___|  / \  / ___|  | |_ ___   ___ | |___                                                                 *;
    *    \___ \ / _ \ \___ \  | __/ _ \ / _ \| / __|                                                                *;
    *     ___) / ___ \ ___) | | || (_) | (_) | \__ \                                                                *;
    *    |____/_/   \_\____/   \__\___/ \___/|_|___/                                                                *;
    *                                                                                                               *;
    *   https://raw.githubusercontent.com/rogerjdeangelis/CostReports/master/cst_010.sas                            *;
    *                                                                                                               *;
    *   Package cst_010.sas                                                                                         *;
    *                                                                                                               *;
    *   Collection of abour 40 tools (sas macros)                                                                   *;
    *                                                                                                               *;
    *                                                                                                               *;
    * ==============================================================================================================*;
    *                                                                                                               *;
    * INTERNAL MACROS                                                                                               *;
    * ===============                                                                                               *;
    *                                                                                                               *;
    *  cln_100makedefine   make define.xml                                                                          *;
    *                                                                                                               *;
    *  cln_110mkesdtmdy    cdisc tools                                                                              *;
    *  cln_120mkesrtodr                                                                                             *;
    *  cln_130cfb                                                                                                   *;
    *  cln_140mkeMtyTbl                                                                                             *;
    *  cln_150domainx                                                                                               *;
    *  cln_160dtc2dt                                                                                                *;
    *  cln_170mrgsup                                                                                                *;
    *                                                                                                               *;
    *  cln_500sdtmDm       create stdm datasets                                                                     *;
    *  cln_525sdtmAe                                                                                                *;
    *  cln_550sdtmEx                                                                                                *;
    *  cln_575sdtmLb                                                                                                *;
    *  cln_600sdtmXp                                                                                                *;
    *                                                                                                               *;
    *  cln_615adamAdsl     create aam datasets                                                                      *;
    *  cln_625adamAdae                                                                                              *;
    *  cln_650adamAdef                                                                                              *;
    *  cln_675adamAdTte                                                                                             *;
    *                                                                                                               *;
    *                                                                                                               *;
    * EXTERNAL MACROS IN AUTOCALL LIBRARY                                                                           *;
    * ====================================                                                                          *;
    *  Utility and performance macros in                                                                            *;
    *                                                                                                               *;
    *  cln_010.sas  (package of 40+ utilties)                                                                       *;
    *                                                                                                               *;
    *                                                                                                               *;
    * PINNACLE (You can test the v5 xport files with pinnacle - interatively)                                       *;
    * ============================================================                                                  *;
    *  Download Pinnacle 21                                                                                         *
    *                                                                                                               *;
    *    https://www.c.com/projects/installing-opencdisc-community                                                  *;
    *                                                                                                               *;
    *    Create parent folder and subfolders                                                                        *;
    *                                                                                                               *;
    *      global_root/pin  (set up for Pinnacle )                                                                  *;
    *                                                                                                               *;
    *         Cfg        * for pinnacle config files                                                                *;
    *         Txt        * for pinnacle report file                                                                 *;
    *         Xml        * for pinnacle xml files                                                                   *;
    *                                                                                                               *;
    *  SAS Proc CDISC                                                                                               *;
    *  ==============                                                                                               *;
    *                                                                                                               *;
    *  There is proc cdisc code you can run to check the SDTM DM sas Dataset                                        *;
    *                                                                                                               *;
    /*  PROC CDISC MODEL = SDTM;                                                                                    */;
    /*   SDTM SDTMVersion = "3.1";                                                                                  */;
    /*   DOMAINDATA data = cln.Dm                                                                                   */;
    /*   domain = DM                                                                                                */;
    /*   category = SPECIAL;                                                                                        */;
    /*  run;quit;                                                                                                   */;
    *                                                                                                               *;
    *  _ __  _ __ ___   ___ ___  ___ ___                                                                            *;
    * | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                                                           *;
    * | |_) | | | (_) | (_|  __/\__ \__ \                                                                           *;
    * | .__/|_|  \___/ \___\___||___/___/                                                                           *;
    * |_|                                                                                                           *;
    *                                                                                                               *;
    *    1. define root directory                                                                                   *;
    *                                                                                                               *;
    *    2. assign libname to root directory                                                                        *;
    *                                                                                                               *;
    *    3. create autocall subdirectory                                                                            *;
    *                                                                                                               *;
    *    4. create directory structure                                                                              *;
    *                                                                                                               *;
    *    5. copy sas tools from the GitHub into OTO(autocall) subdirectory                                          *;
    *                                                                                                               *;
    *    6. compile tools into work.sasmacr                                                                         *;
    *                                                                                                               *;
    *    7. create formats                                                                                          *;
    *                                                                                                               *;
    *    8. copy excel workbooks from the GiHub (key step)                                                          *;
    *                                                                                                               *;
    *    9. create stdmDefine.xml and adamDefine.xml                                                                *;
    *                                                                                                               *;
    *   10. copy raw crf sas datasets from the GitHub                                                               *;
    *                                                                                                               *;
    *   11. ceate sdtm datasets and associated V5 export files                                                      *;
    *                                                                                                               *;
    *        ae.sas7bdat       cln_sdtmAe.xpt                                                                       *;
    *        dm.sas7bdat       cln_sdtmdm.xpt                                                                       *;
    *        ex.sas7bdat       cln_sdtmLb.xpt                                                                       *;
    *        lb.sas7bdat       cln_sdtmsEx.xpt                                                                      *;
    *        suppdm.sas7bdat   cln_sdtmsuppdm.xpt                                                                   *;
    *        xp.sas7bdat       cln_sdtmsXp.xpt                                                                      *;
    *                                                                                                               *;
    *   12. ceate adam datasets and associated V5 export files                                                      *;
    *                                                                                                               *;
    *        adae.sas7bdat     cln_adamadae.xpt                                                                     *;
    *        adef.sas7bdat     cln_adamadef.xpt                                                                     *;
    *        adsl.sas7bdat     cln_adamadsl.xpt                                                                     *;
    *        adtte.sas7bdat    cln_adamadtte.xpt                                                                    *;
    *                                                                                                               *;
    *                _               _                                                                              *;
    *     ___  _   _| |_ _ __  _   _| |_                                                                            *;
    *    / _ \| | | | __| `_ \| | | | __|                                                                           *;
    *   | (_) | |_| | |_| |_) | |_| | |_                                                                            *;
    *    \___/ \__,_|\__| .__/ \__,_|\__|                                                                           *;
    *                   |_|                                                                                         *;
    *                                                                                                               *;
    *    THE PRIMARY OUTPUTS ARE THE  ADaM and SDTM, DEFINE.XMLs, DATASETS, AND XPORT FILES                         *;
    *                                                                                                               *;
    *    FINAL STATE OF DIRECTORIES                                                                                 *;
    *                                                                                                               *;
    *    I-INPUT FROM GITHUB O= OUTPUT                                                                              *;
    *                                                                                                               *;
    *    Directory of &gbl_root/cln                                                                                 *;
    *                                                                                                               *;
    *      O cln_fmt.sas7bcat            * formats                                                                  *;
    *                                                                                                               *;
    *      I cln_rawadverse.sas7bdat     * raw data                                                                 *;
    *      I cln_rawdemographic.sas7bdat                                                                            *;
    *      I cln_rawdosing.sas7bdat                                                                                 *;
    *      I cln_rawlabs.sas7bdat                                                                                   *;
    *      I cln_rawpain.sas7bdat                                                                                   *;
    *                                                                                                               *;
    *      O adae.sas7bdat   * adams;                                                                               *;
    *      O adef.sas7bdat                                                                                          *;
    *      O adsl.sas7bdat                                                                                          *;
    *      O adtte.sas7bdat                                                                                         *;
    *                                                                                                               *;
    *      O ae.sas7bdat      *sdtms;                                                                               *;
    *      O dm.sas7bdat                                                                                            *;
    *      O ex.sas7bdat                                                                                            *;
    *      O lb.sas7bdat                                                                                            *;
    *      O suppdm.sas7bdat                                                                                        *;
    *      O xp.sas7bdat                                                                                            *;
    *                                                                                                               *;
    *       <DIR>  b64                                                                                              *;
    *                I cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;                        *;
    *                I cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;                        *;
    *                                                                                                               *;
    *                I cln_rawadverse.b64     * base 64 encoded raw sas datasets;                                   *;
    *                I cln_rawdemographic.b64                                                                       *;
    *                I cln_rawdosing.b64                                                                            *;
    *                I cln_rawlabs.b64                                                                              *;
    *                I cln_rawpain.b64                                                                              *;
    *       <DIR>  log                                                                                              *;
    *       <DIR>  oto                                                                                              *;
    *                I cln_010.sas            * many macro tools;                                                   *;
    *                                                                                                               *;
    *                I cln_100makedefine.sas  * CDISC utilities ( extract meta data from excel tabs;                *;
    *                I cln_110mkesdtmdy.sas                                                                         *;
    *                I cln_120mkesrtodr.sas                                                                         *;
    *                I cln_130cfb.sas                                                                               *;
    *                I cln_140mkeMtyTbl.sas                                                                         *;
    *                I cln_150domainx.sas                                                                           *;
    *                I cln_160dtc2dt.sas                                                                            *;
    *                I cln_170mrgsup.sas                                                                            *;
    *                                                                                                               *;
    *                I cln_500sdtmDm.sas     * create sdtm datasets DM;                                             *;
    *                I cln_525sdtmAe.sas     * AE                                                                   *;
    *                I cln_550sdtmEx.sas     * EX                                                                   *;
    *                I cln_575sdtmLb.sas     * LB                                                                   *;
    *                I cln_600sdtmXp.sas     * XP                                                                   *;
    *                                                                                                               *;
    *                I cln_615adamAdsl.sas   * create adam datasets ADSL                                            *;
    *                I cln_625adamAdae.sas   * ADSL                                                                 *;
    *                I cln_650adamAdef.sas   * ADAE                                                                 *;
    *                I cln_675adamAdTte.sas  * ADEF                                                                 *;
    *                                                                                                               *;
    *       <DIR>  pdf                                                                                              *;
    *       <DIR>  pin                                                                                              *;
    *       <DIR>  png                                                                                              *;
    *       <DIR>  rtf                                                                                              *;
    *       <DIR>  sas                                                                                              *;
    *                                                                                                               *;
    *       <DIR>  xls                                                                                              *;
    *                O cln_adamMetadata.xlsx  * excel mata data for adam datasets;                                  *;
    *                O cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;                                  *;
    *                                                                                                               *;
    *       <DIR>  xml                                                                                              *;
    *                                                                                                               *;
    *                O cln_adamDefine.xml           * concatenation of all odm sections below                       *;
    *                                                                                                               *;
    *                O cln_adamdefine_header.xml    * excel odm sections created from excel tab header              *;
    *                O cln_adamanalysisresults.xml  ..                                                              *;
    *                O cln_adamcompmethod.xml                                                                       *;
    *                O cln_adamitemdef.xml                                                                          *;
    *                O cln_adamitemdef_value.xml                                                                    *;
    *                O cln_adamitemgroupdef.xml                                                                     *;
    *                O cln_adamleaves.xml                                                                           *;
    *                O cln_adamvaluelist.xml                                                                        *;
    *                O cln_sdtmcompmethod.xml                                                                       *;
    *                                                                                                               *;
    *                O cln_sdtmDefine.xml         * concatenation of all stdm sections below                        *;
    *                                                                                                               *;
    *                O cln_sdtmdefine_header.xml  * excel odm section created from excel tab header                 *;
    *                O cln_sdtmitemdef.xml        ..                                                                *;
    *                O cln_sdtmitemdef_value.xml                                                                    *;
    *                O cln_sdtmitemgroupdef.xml                                                                     *;
    *                O cln_sdtmvaluelist.xml                                                                        *;
    *       <DIR>  xpt                                                                                              *;
    *                O cln_adamadtte.xpt                                                                            *;
    *                O cln_adamadae.xpt                                                                             *;
    *                O cln_adamadef.xpt                                                                             *;
    *                O cln_adamadsl.xpt                                                                             *;
    *                                                                                                               *;
    *                O cln_sdtmAe.xpt                                                                               *;
    *                O cln_sdtmdm.xpt                                                                               *;
    *                O cln_sdtmsEx.xpt                                                                              *;
    *                O cln_sdtmsuppdm.xpt                                                                           *;
    *                O cln_sdtmsXp.xpt                                                                              *;
    *                                                                                                               *;
    *                                                                                                               *;
    *******************************************************************************************************************;
    *                                                                                                                 *;
    *  Version History                                                                                                *;
    *                                                                                                                 *;
    *  Version     Date             Programmer    Description                                                         *;
    *  -------     ---------        ----------    -----------                                                         *;
    *   1.0        24JAN2021        rdeangel      creation                                                            *;
    *                                                                                                                 *;
    *                                                                                                                 *;
    *******************************************************************************************************************;
 
 
    /*___  _____ _____ _   _ ____
    / ___|| ____|_   _| | | |  _ \
    \___ \|  _|   | | | | | | |_) |
     ___) | |___  | | | |_| |  __/
    |____/|_____| |_|  \___/|_|
 
    */
 
    %symdel
        gbl_root
        gbl_tools;
 
    %let gbl_root =d:/cln;   * autocall library;
    %let gbl_tools=https://raw.githubusercontent.com/rogerjdeangelis/CostReports/master/cst_010.sas;
 
    %put &=gbl_root;
    %put &=gbl_tools;
 
    * create folder even if it does not exist;
    options dlcreatedir;
      libname  cln "&gbl_root";
    options nodlcreatedir;
 
    /* Just in case you rerun */
 
    proc datasets lib=work kill;
    run;quit;
 
    proc catalog cat=work.sasmacr et=macro;
     delete
        cln_100makedefine
        cln_110mkesdtmdy
        cln_120mkesrtodr
        cln_130cfb
        cln_140mkeMtyTbl
        cln_150domainx
        cln_160dtc2dt
        cln_170mrgsup
        cln_500sdtmDm
        cln_525sdtmAe
        cln_550sdtmEx
        cln_575sdtmLb
        cln_600sdtmXp
        cln_615adamAdsl
        cln_625adamAdae
        cln_650adamAdef
        cln_675adamAdTte
    ;run;quit;
 
    %utlfkil(&gbl_root/oto/cln_100makedefine.sas);
    %utlfkil(&gbl_root/oto/cln_110mkesdtmdy.sas);
    %utlfkil(&gbl_root/oto/cln_120mkesrtodr.sas);
    %utlfkil(&gbl_root/oto/cln_130cfb.sas);
    %utlfkil(&gbl_root/oto/cln_140mkeMtyTbl.sas);
    %utlfkil(&gbl_root/oto/cln_150domainx.sas);
    %utlfkil(&gbl_root/oto/cln_160dtc2dt.sas);
    %utlfkil(&gbl_root/oto/cln_170mrgsup.sas);
    %utlfkil(&gbl_root/oto/cln_500sdtmDm.sas);
    %utlfkil(&gbl_root/oto/cln_525sdtmAe.sas);
    %utlfkil(&gbl_root/oto/cln_550sdtmEx.sas);
    %utlfkil(&gbl_root/oto/cln_575sdtmLb.sas);
    %utlfkil(&gbl_root/oto/cln_600sdtmXp.sas);
    %utlfkil(&gbl_root/oto/cln_615adamAdsl.sas);
    %utlfkil(&gbl_root/oto/cln_625adamAdae.sas);
    %utlfkil(&gbl_root/oto/cln_650adamAdef.sas);
    %utlfkil(&gbl_root/oto/cln_675adamAdTte.sas);
 
    proc datasets lib=cln nolist;
      delete
          adae
          adef
          adsl
          adtte
          ae
          cln_rawadverse
          cln_rawdemographic
          cln_rawdosing
          cln_rawlabs
          cln_rawpain
          dm
          ex
          lb;
    run;quit;
 
    libname sdtm  "&gbl_root";
    libname adam  "&gbl_root";
 
    data _null_;
        length newdir $64;
        newdir=dcreate('oto',"&gbl_root");  /* autocall folder */
    run;quit;
 
    options fmtsearch=(cln.cln_fmt work.formats)  xwait xsync
             sasautos=("&gbl_root./oto", sasautos) ;
 
    /*
     Folders so far
 
     Directory of d:\cln
 
        <DIR>          oto * autocall library
 
    /*                   _          __       _     _
      ___ _ __ ___  __ _| |_ ___   / _| ___ | | __| | ___ _ __ ___
     / __| `__/ _ \/ _` | __/ _ \ | |_ / _ \| |/ _` |/ _ \ `__/ __|
    | (__| | |  __/ (_| | ||  __/ |  _| (_) | | (_| |  __/ |  \__ \
     \___|_|  \___|\__,_|\__\___| |_|  \___/|_|\__,_|\___|_|  |___/
 
    */
 
    * create root folder for sas datasets;
 
    * create subfolders;
    data _null_;
 
        length newdir $64;
 
        newdir=dcreate('oto',"&gbl_root");  /* autocall folder */
        newdir=dcreate('xls',"&gbl_root");  /* input excel image of define xml - key metatdata */
        newdir=dcreate('pdf',"&gbl_root");  /* documentation */
        newdir=dcreate('zip',"&gbl_root");  /* zipped saves */
        newdir=dcreate('rtf',"&gbl_root");  /* tables and listings */
        newdir=dcreate('png',"&gbl_root");  /* figures */
        newdir=dcreate('log',"&gbl_root");  /* batch logs */
        newdir=dcreate('b64',"&gbl_root");  /* base 64 binaries from github - github does not support auto downloads */
        newdir=dcreate('sas',"&gbl_root");  /* base 64 binaries from github - github does not support auto downloads */
        newdir=dcreate('xml',"&gbl_root");  /* define xml and define.xml odm sections */
        newdir=dcreate('xpt',"&gbl_root");  /* SAS V5 export */
        newdir=dcreate('pin',"&gbl_root");  /* pinacle 21 */
 
    run;quit;
 
    /* OUTPUT DIRECTORY STRUCTURE
 
    Directory of &gbl_root
 
       <DIR>  b64
       <DIR>  log
       <DIR>  oto   ** autocall library
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
       <DIR>  xls
       <DIR>  xml
       <DIR>  xpt
       <DIR>  zip
    */
 
    /*          _     _              _
      __ _  ___| |_  | |_ ___   ___ | |___
     / _` |/ _ \ __| | __/ _ \ / _ \| / __|
    | (_| |  __/ |_  | || (_) | (_) | \__ \
     \__, |\___|\__|  \__\___/ \___/|_|___/
     |___/
    */
 
    filename _bcot "&gbl_root/oto/cln_010.sas";
    proc http
       method='get'
       url="&gbl_tools"
       out= _bcot;
    run;quit;
 
    /* OUTPUT
 
    Directory of &gbl_root
 
       <DIR>  b64
       <DIR>  log
       <DIR>  oto
               cln_010.sas * many macro tools;
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  xls
       <DIR>  xml
       <DIR>  xpt
       <DIR>  zip
    */
 
    /*                         _ _        _              _
      ___ ___  _ __ ___  _ __ (_) | ___  | |_ ___   ___ | |___
     / __/ _ \| `_ ` _ \| `_ \| | |/ _ \ | __/ _ \ / _ \| / __|
    | (_| (_) | | | | | | |_) | | |  __/ | || (_) | (_) | \__ \
     \___\___/|_| |_| |_| .__/|_|_|\___|  \__\___/ \___/|_|___/
                        |_|
    */
 
    * compile utility macros;
    filename cin "&gbl_root./oto/cln_010.sas" lrecl=4096 recfm=v;
    %inc cin / nosource;
 
    /*__                            _
     / _| ___  _ __ _ __ ___   __ _| |_ ___
    | |_ / _ \| `__| `_ ` _ \ / _` | __/ __|
    |  _| (_) | |  | | | | | | (_| | |_\__ \
    |_|  \___/|_|  |_| |_| |_|\__,_|\__|___/
 
    */
 
    proc format lib=cln.cln_fmt;
 
      value visit_labs_month
                     0=baseline
                     1=3 months
                     2=6 months;
 
      value _0n1y 0 = 'N'
                  1 = 'Y'
      ;
      value avisitn 1 = '3'
                    2 = '6'
      ;
      value popfl 0 - high = 'Y'
                  other = 'N'
      ;
      value $trt01pn  'Analgezia HCL 30 mg' = '1'
                      'Placebo'             = '0'
      ;
      value agegr1n 0 - 54 = "1"
                    55-high= "2"
      ;
      value agegr1_ 1 = "<55 YEARS"
                    2 = ">=55 YEARS"
      ;
      value $aereln  'NOT'        = '0'
                     'POSSIBLY'   = '1'
                     'PROBABLY'   = '2'
      ;
      value $aesevn  'MILD'               = '1'
                     'MODERATE'           = '2'
                     'SEVERE'             = '3'
      ;
      value relgr1n 0 = 'NOT RELATED'
                    1 = 'RELATED'
      ;
      value evntdesc 0 = 'PAIN RELIEF'
                     1 = 'PAIN WORSENING PRIOR TO RELIEF'
                     2 = 'PAIN ADVERSE EVENT PRIOR TO RELIEF'
                     3 = 'COMPLETED STUDY PRIOR TO RELIEF'
      ;
    run;
 
    /*
    Note: Formatds are not sent to FDA we provide code/decode pairs
 
    proc catalog cat=cln.cln_fmt;
    contents;
    run;quit;
 
    Contents of Catalog CLN.CLN_FMT
 
     #    Name                Type
    ------------------------------------
     1    AGEGR1N             FORMAT
     2    AGEGR1_             FORMAT
     3    AVISITN             FORMAT
     4    EVNTDESC            FORMAT
     5    POPFL               FORMAT
     6    RELGR1N             FORMAT
     7    VISIT_LABS_MONTH    FORMAT
     8    _0N1Y               FORMAT
     9    AERELN              FORMATC
    10    AESEVN              FORMATC
    11    TRT01PN             FORMATC
    */
 
    /*          _          _                      _    _                 _
      __ _  ___| |_  __  _| |_  __ __      ___ __| | _| |__   ___   ___ | | _____
     / _` |/ _ \ __| \ \/ / \ \/ / \ \ /\ / / `__| |/ / `_ \ / _ \ / _ \| |/ / __|
    | (_| |  __/ |_   >  <| |>  <   \ V  V /| |  |   <| |_) | (_) | (_) |   <\__ \
     \__, |\___|\__| /_/\_\_/_/\_\   \_/\_/ |_|  |_|\_\_.__/ \___/ \___/|_|\_\___/
     |___/
    */
 
    * DOWNLOAD EXCEL WOOKBOOKS WITH META DATA;
 
    * you can download the workbooks manually and place in /cln/xls or run these scripts;
    * The code below downloads the base64 text and converts the text to binary zipped xml ;
 
    * Only if you need to encode the excel workbooks in bas64 for upload/download from GitHub. Bas64 is text;
    %*utl_b64encode(&gbl_root/xls/cln_adamMetadata.xlsx,&gbl_root/b64/cln_adamMetadata.b64); /*only if I need to upload another newer version of the data */
    %*utl_b64encode(&gbl_root/xls/cln_sdtmMetadata.xlsx,&gbl_root/b64/cln_sdtmMetadata.b64); /*only if I need to upload another newer version of the data */
 
 
    * download cln.sdtmMetadata.b64 and convert to sdtmMetadata.xlsx;
    filename download clear;
    filename download "&gbl_root/b64/cln_sdtmMetadata.b64";
    proc http
       method='GET'
       url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_sdtmMetadata.b64"
       out=download;
    run;quit;
    filename download clear;
    %utl_b64decode(&gbl_root/b64/cln_sdtmMetadata.b64,&gbl_root/xls/cln_sdtmMetadata.xlsx);
 
    * download cln.adamMetadata.b64 and convert to adamMetadata.xlsx;
    filename download clear;
    filename download "&gbl_root/b64/cln_adamMetadata.b64";
    proc http
       method='GET'
       url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_adamMetadata.b64"
       out=download;
    run;quit;
    filename download clear;
    %utl_b64decode(&gbl_root/b64/cln_adamMetadata.b64,&gbl_root/xls/cln_adamMetadata.xlsx);
    run;quit;
 
    /* structure
 
    - added files
 
    Directory of &gbl_root/cln
 
        cln_fmt.sas7bcat
                                         * formats
       <DIR>  b64
                - cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                - cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
 
       <DIR>  xls
                - cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                - cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
       <DIR>  xpt
       <DIR>  zip
    */
 
    /*   _       __ _                             _
      __| | ___ / _(_)_ __   ___  __  ___ __ ___ | |
     / _` |/ _ \ |_| | `_ \ / _ \ \ \/ / `_ ` _ \| |
    | (_| |  __/  _| | | | |  __/  >  <| | | | | | |
     \__,_|\___|_| |_|_| |_|\___| /_/\_\_| |_| |_|_|
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_100makedefine.sas";
    parmcards4;
    %macro cln_100makedefine(path=,metadata=,out=);
    /*---------------------------------------------------------------*;
    * %make_define generates parts of the define.xml file for the SDTM and ADaM.
    * The parts can be concatenated into the define.xml file using shell scripts.
    *
    * PARAMETERS:
    *            path = System path to where the SDTM or ADaM metadata
    *                   file exists as well as where the define.xml
    *                   file will be stored.
    *        metadata = The name of the metadata spreadsheet.
    *
    * It requires that the following tabs exist in the metadata file:
    * DEFINE_HEADER_METADATA = define file header metadata
    * TOC_METADATA           = "table of contents" dataset metadata
    * VARIABLE_METADATA      = variable/column level metadata
    * VALUELEVEL_METADATA    = value/parameter level metadata
    * COMPUTATIONAL_MKETHOD  = computational methods
    * CODELISTS              = controlled terminology metadata
 
    * ANALYSIS_RESULTS       = ADaM analysis metadata. [Only for ADaM define]
    * EXTERNAL_LINKS         = ADaM results file pointers. [Only for ADaM define]
 
 
    INPUT
    =====
    TWO EXECL WORKBOOKS WITH THE SHEETD THAT MAP TO THE DEFINE XLM ODM SECTIONS
 
      cln_sdtmMetadata.xlsx
        sheets
            DEFINE_HEADER_METADATA
            TOC_METADATA
            VARIABLE_METADATA
            VALUELEVEL_METADATA
            COMPUTATIONAL_MKETHOD
            CODELISTS
 
      cln_adamMetadata.xlsx
        sheets
 
            DEFINE_HEADER_METADATA
            TOC_METADATA
            VARIABLE_METADATA
            VALUELEVEL_METADATA
            COMPUTATIONAL_MKETHOD
            CODELISTS
            ANALYSIS_RESULTS
            EXTERNAL_LINKS
 
    OUTPUT
    ======
 
    Directory of d:\cln\xml
    -----------------------
 
     cln_sdtmDefine.xml             62,255  SDTM DEFINE XLM
     cln_adamDefine.xml             47,905  ADAM DEFILE XML
 
     cln_adamanalysisresults.xml     8,700  concatenate these 8 xlm sections into cln_adamDefine.xml
     cln_adamcompmethod.xml            385
     cln_adamdefine_header.xml       1,738
     cln_adamitemdef.xml            21,165
     cln_adamitemdef_value.xml       1,118
     cln_adamitemgroupdef.xml       12,744
     cln_adamleaves.xml              1,463
     cln_adamvaluelist.xml             592
 
     cln_sdtmcompmethod.xml            290  concatenate these 6 xlm sections into cln_sdtmDefine.xml
     cln_sdtmdefine_header.xml       1,677
     cln_sdtmitemdef.xml            27,026
     cln_sdtmitemdef_value.xml       2,778
     cln_sdtmitemgroupdef.xml       29,536
     cln_sdtmvaluelist.xml             948
 
    */
 
    **** GET DEFINE FILE HEADER INFORMATION METADATA;
    proc import
        out = define_header
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet="DEFINE_HEADER_METADATA";
    run;
 
    **** DETERMINE IF THIS IS A SDTM DEFINE FILE OR AN ADAM DEFINE FILE
    **** AND SET THE STANDARD MACRO VARIABLE FOR THE REST OF THE PROGRAM;
    data _null_;
          set define_header;
 
        if upcase(standard) = 'ADAM' then
            call symput('standard','ADAM');
        else if upcase(standard) = 'SDTM' then
            call symput('standard','SDTM');
        else
            put "ERR" "OR: CDISC standard undefined in define_header_metadata";
    run;
 
    **** GET "TABLE OF CONTENTS" LEVEL DATASET METADATA;
    proc import
        out = toc_metadata
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet = "TOC_METADATA" ;
    run;
 
    **** GET THE VARIABLE METADATA;
    proc import
        out = VARIABLE_METADATA
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet = "VARIABLE_METADATA";
    run;
 
    **** GET THE CODELIST METADATA;
    proc import
        out = codelists
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet = "CODELISTS" ;
    run;
 
    **** GET THE COMPUTATIONAL METHOD METADATA;
    proc import
        out = compmethod
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet = "COMPUTATION_METHOD" ;
    run;
 
    **** GET THE VALUE LEVEL METADATA;
    proc import
        out = valuelevel
        datafile = "&path\&metadata"
        dbms=xlsx
        replace;
        sheet = "VALUELEVEL_METADATA" ;
    run;
 
    %if "&standard" = "ADAM" %then
      %do;
        **** GET THE ANALYSIS RESULTS METADATA;
        proc import
            out = analysisresults
            datafile = "&path\&metadata"
            dbms=xlsx
            replace;
            sheet = "ANALYSIS_RESULTS" ;
        run;
 
        **** GET THE ANALYSIS RESULTS METADATA;
        proc import
            out = externallinks
            datafile = "&path\&metadata"
            dbms=xlsx
            replace;
            sheet = "EXTERNAL_LINKS" ;
        run;
      %end;
 
    **** USE HTMLENCODE ON SOURCE TEXT THAT NEEDS ENCODING FOR PROPER BROWSER REPRESENTATIION;
    %if &standard=ADAM %then
      %do;
 
        data toc_metadata;
                length documentation $ 800;
                set toc_metadata;
 
              documentation = htmlencode(documentation);
              ** convert single quotes to double quotes;
              documentation = tranwrd(documentation, "'", '"');
              ** convert double quotes to html quote;
              documentation = tranwrd(trim(documentation), '"', '&quot;');
              format documentation $800.;
        run;
 
      %end;
 
 
    data variable_metadata;
          length comment $ 2000;
          set variable_metadata;
 
          format comment;
          informat comment;
        origin = htmlencode(origin);
          label = htmlencode(label);
          comment = htmlencode(comment);
 
        **** FOR ADAM, JOIN ORIGIN/"SOURCE" AND COMMENT
          **** TO FORM "SOURCE/DERIVATION" METADATA;
          if "&standard" = "ADAM" and origin ne '' and
            comment ne '' then
          comment = "SOURCE: " || left(trim(origin)) ||
                    " DERIVATION: " || left(trim(comment));
          else if "&standard" = "ADAM" and origin ne '' and
            comment = '' then
          comment = "SOURCE: " || left(trim(origin));
          if "&standard" = "ADAM" and origin = '' and
            comment ne '' then
          comment = "DERIVATION: " || left(trim(comment));
    run;
 
    data codelists;
          set codelists;
 
          codedvalue = htmlencode(codedvalue);
          translated = htmlencode(translated);
    run;
 
    data compmethod;
          set compmethod;
 
          computationmethod = htmlencode(computationmethod);
    run;
 
    data valuelevel;
          length comment $ 2000;
          set valuelevel;
 
          format comment;
          informat comment;
        origin = htmlencode(origin);
          label = htmlencode(label);
          comment = htmlencode(comment);
 
        **** FOR ADAM, JOIN ORIGIN/"SOURCE" AND COMMENT
          **** TO FORM "SOURCE/DERIVATION" METADATA;
          if "&standard" = "ADAM" and origin ne '' and
            comment ne '' then
          comment = "SOURCE: " || left(trim(origin)) ||
                    " DERIVATION: " || left(trim(comment));
          else if "&standard" = "ADAM" and origin ne '' and
            comment = '' then
          comment = "SOURCE: " || left(trim(origin));
          if "&standard" = "ADAM" and origin = '' and
            comment ne '' then
          comment = "DERIVATION: " || left(trim(comment));
    run;
 
 
    %if "&standard" = "ADAM" %then
      %do;
        data analysisresults;
             length programmingcode $800. docleafid $40.;
            set analysisresults;
          where displayid ne '';
 
          arrow + 1;
          selectioncriteria = htmlencode(selectioncriteria);
          paramlist = htmlencode(paramlist);
          reason = htmlencode(reason);
          documentation = htmlencode(documentation);
          if index(documentation, '[r]')>0 then
            docleafid = substr(documentation, index(documentation,'[r]')+3, index(documentation,'[\r]')-index(documentation,'[r]')-3);
          else
            docleafid = '.';
 
          programmingcode = htmlencode(programmingcode);
          ** convert single quotes to double quotes;
          programmingcode = tranwrd(programmingcode, "'", '"');
          ** convert double quotes to html quote;
          programmingcode = tranwrd(programmingcode, '"', '&quot;');
          format programmingcode $800.;
        run;
 
        ** ENSURE UNIQUENESS ON DISPLAYID AND RESULTID AND CREATE A COMBO ID;
        data analysisresults;
          set analysisresults;
          by displayid notsorted;
 
          drop resultnum;
          retain resultnum;
          if first.displayid then
              resultnum = 0;
          resultnum + 1;
          if not(first.displayid and last.displayid) then
              arid = trim(displayid) || ".R." || put(resultnum,z2.);
          else
              arid = displayid;
        run;
 
        ** IF DOCLEAFID IS NON-MISSING, MERGE IN THE TITLE FROM EXTERNAL_LINKS;
        proc sort
          data = analysisresults;
          by docleafid;
        run;
 
        proc sort
          data = externallinks (keep = leafid title rename=(leafid=docleafid title=doctitle))
          out  = doc_links;
          by docleafid;
        run;
 
        data analysisresults;
          merge analysisresults (in = inar) doc_links (in = indoc_links);
          by docleafid;
 
          if inar;
          ** if the leaf ID exists, then the title of the leaf ID will be printed and can be removed from DOCUMENTIATION;
          if indoc_links then
            documentation = tranwrd(documentation, '[r]' || trim(docleafid) || '[\r]', " ");
        run;
 
        proc sort
          data = analysisresults;
          by arrow;
        run;
      %end;
 
    **** CREATE DEFINE FILE HEADER SECTION;
    filename dheader "&out.define_header.xml";
    data define_header;
        set define_header;
 
        file dheader notitles;
 
          creationdate = compress(put(datetime(), IS8601DT.));
 
        put @1 '<?xml version="1.0" encoding="ISO-8859-1" ?>' /
            @1 '<?xml-stylesheet type="text/xsl" href="' stylesheet +(-1) '"?>' /
            @1 '<!-- ******************************************************************************* -->' /
            @1 '<!-- File: define.xml                                                                -->' /
            @1 "<!-- Date: &sysdate9.                                                                -->" /
            @1 '<!-- Description: Define.xml file for '   studyname +(-1) '                          -->' /
            @1 '<!-- ******************************************************************************* -->' /
            @1 '<ODM' /
            @3 'xmlns="http://www.cdisc.org/ns/odm/v1.2"' /
            @3 'xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"' /
            @3 'xmlns:xlink="http://www.w3.org/1999/xlink"' /
            @3 'xmlns:def="http://www.cdisc.org/ns/def/v1.0"' /
            %if "&standard" = "ADAM" %then
              @3 'xmlns:adamref="http://www.cdisc.org/ns/ADaMRes/DRAFT"' /
            ;
            @3 'xsi:schemaLocation="' schemalocation +(-1) '"' /
            @3 'FileOID="' fileoid +(-1) '"' /
            @3 'ODMVersion="1.2"' /
            @3 'FileType="Snapshot"' /
            @3 'CreationDateTime="' creationdate +(-1) '">' /
            @1 '<Study OID="' studyoid +(-1) '">' /
            @3 '<GlobalVariables>' /
            @5 '<StudyName>' studyname +(-1) '</StudyName>' /
            @5 '<StudyDescription>' studydescription +(-1) '</StudyDescription>' /
            @5 '<ProtocolName>' protocolname +(-1) '</ProtocolName>' /
            @3 '</GlobalVariables>' /
            @3 '<MetaDataVersion OID="CDISC.' standard +(-1) '.' version +(-1) '"' /
            @5 'Name="' studyname +(-1) ',Data Definitions"' /
            @5 'Description="' studyname +(-1) ',Data Definitions"' /
            @5 'def:DefineVersion="1.0.0"' /
            @5 'def:StandardName="CDISC ' standard +(-1) '"' /
            @5 'def:StandardVersion="' version +(-1) '">' /
            %if "&standard" = "ADAM" %then
              %do;
                @5 '<def:SupplementalDoc>' /
                @7 '<def:DocumentRef leafID="Suppdoc"/>' /
                @5 '</def:SupplementalDoc>' /
                @5 '<def:leaf ID="Suppdoc" xlink:href="dataguide.pdf">' /
                @7 '<def:title>Data Guide</def:title>' /
              %end;
            %else %if "&standard" = "SDTM" %then
              %do;
                @5 '<def:AnnotatedCRF>' /
                @7 '<def:DocumentRef leafID="blankcrf"/>' /
                @5 '</def:AnnotatedCRF>' /
                @5 '<def:leaf ID="blankcrf" xlink:href="blankcrf.pdf">' /
                @7 '<def:title>Annotated Case Report Form</def:title>' /
              %end;
            @5 '</def:leaf>';
    run;
 
    **** ADD OTHER ADAM EXTERNAL LINKS;
    %if "&standard" = "ADAM" %then
      %do;
        filename leaves "&out.leaves.xml";
        data _null_;
          set externallinks;
 
          file leaves notitles;
 
          put @5 '<def:leaf ID="' leafid +(-1) '"'     /
              @7 'xlink:href="' leafrelpath +(-1) '">' /
              @7 '<def:title>' title '</def:title>'    /
              @5 '</def:leaf>'
              ;
        run;
      %end;
 
 
    **** ADD ITEMOID TO VARIABLE METADATA;
    data variable_metadata;
        set variable_metadata(rename=(domain = oid));
 
        length itemoid $ 40;
        if variable in ("STUDYID","DOMAIN","USUBJID","SUBJID") then
          itemoid = variable;
        else
          itemoid = compress(oid || "." || variable);
    run;
 
    **** ADD ITEMOID TO VALUE LEVEL METADATA;
    data valuelevel;
        set valuelevel;
 
        length itemoid $ 200;
        itemoid = compress(valuelistoid || "." || valuename);
    run;
 
    **** CREATE COMPUTATION METHOD SECTION;
    filename comp "&out.compmethod.xml";
    data compmethods;
        set compmethod;
 
        file comp notitles;
 
        if _n_ = 1 then
        put @5 "<!-- ******************************************* -->" /
            @5 "<!-- COMPUTATIONAL METHOD INFORMATION        *** -->" /
            @5 "<!-- ******************************************* -->";
        put @5 '<def:ComputationMethod OID="' computationmethodoid +(-1) '">' computationmethod +(-1) '</def:ComputationMethod>';
    run;
 
 
    **** CREATE VALUE LEVEL LIST DEFINITION SECTION;
    proc sort
        data=valuelevel;
        where valuelistoid ne '';
        by valuelistoid;
    run;
 
    filename vallist "&out.valuelist.xml";
    data valuelevel;
      set valuelevel;
        by valuelistoid;
 
        file vallist notitles;
 
        if _n_ = 1 then
          put @5 "<!-- ******************************************* -->" /
              @5 "<!-- VALUE LEVEL LIST DEFINITION INFORMATION  ** -->" /
              @5 "<!-- ******************************************* -->";
 
        if first.valuelistoid then
          put @5 '<def:ValueListDef OID="' valuelistoid +(-1) '">';
 
        put @7 '<ItemRef ItemOID="' itemoid  +(-1) '"' /
            @9 'Mandatory="' mandatory +(-1) '"/>';
 
        if last.valuelistoid then
          put @5 '</def:ValueListDef>';
    run;
 
 
 
    **** CREATE "ITEMGROUPDEF" SECTION;
    proc sort
        data=VARIABLE_METADATA;
        where oid ne '';
        by oid varnum;
    run;
 
    proc sort
        data=toc_metadata;
        where oid ne '';
        by oid;
    run;
 
    filename igdef "&out.itemgroupdef.xml";
    data itemgroupdef;
        length label $ 40;
        merge toc_metadata VARIABLE_METADATA(drop=label);
        by oid;
 
        file igdef notitles;
 
        if first.oid then
          do;
            put @5 "<!-- ******************************************* -->" /
                @5 "<!-- " oid   @25   "ItemGroupDef INFORMATION *** -->" /
                @5 "<!-- ******************************************* -->" /
                @5 '<ItemGroupDef OID="' oid +(-1) '"' /
                @7 'Name="' name +(-1) '"' /
                @7 'Repeating="' repeating +(-1) '"' /
                @7 'Purpose="' purpose +(-1) '"' /
                @7 'IsReferenceData="' isreferencedata +(-1) '"' /
                @7 'def:Label="' label +(-1) '"' /
                @7 'def:Structure="' structure +(-1) '"' /
                @7 'def:DomainKeys="' domainkeys +(-1) '"' /
                @7 'def:Class="' class +(-1) '"' ;
            %if &standard=ADAM %then
              put @7 'def:ArchiveLocationID="Location.' archivelocationid +(-1) '"' /
                  @7 'Comment="' documentation +(-1) '">'
                  ;
            %else
              put @7 'def:ArchiveLocationID="Location.' archivelocationid +(-1) '">';
            ;
          end;
 
        put @7 '<ItemRef ItemOID="' itemoid +(-1) '"' /
            @9 'OrderNumber="' varnum +(-1) '"' /
            @9 'Mandatory="' mandatory +(-1) @;
 
        if role ne '' and "&standard" = "SDTM" then
          put '"' /
          @9 'Role="' role +(-1) '"' /
          @9 'RoleCodeListOID="CodeList.' rolecodelist +(-1) '"/>';
        else
          put '"/>';
 
 
        if last.oid then
          put @7 "<!-- **************************************************** -->" /
              @7 "<!-- def:leaf details for hypertext linking the dataset   -->" /
              @7 "<!-- **************************************************** -->" /
              @7 '<def:leaf ID="Location.' oid +(-1) '" xlink:href="' archivelocationid +(-1) '.xpt">' /
              @9 '<def:title>' archivelocationid +(-1) '.xpt </def:title>' /
              @7 '</def:leaf>' /
              @5 '</ItemGroupDef>';
    run;
 
 
    **** CREATE "ITEMDEF" SECTION;
    filename idef "&out.itemdef.xml";
 
    data itemdef;
        set VARIABLE_METADATA end=eof;
        by oid;
 
        file idef notitles;
 
        if _n_ = 1 then
          put @5 "<!-- ************************************************************ -->" /
              @5 "<!-- The details of each variable is here for all domains         -->" /
              @5 "<!-- ************************************************************ -->" ;
 
        put @5 '<ItemDef OID="' itemoid +(-1) '"' /
            @7 'Name="' variable +(-1) '"' /
            @7 'DataType="' type +(-1) '"' /
            @7 'Length="' length +(-1) '"';
        if significantdigits ne '' then
          put @7 'SignificantDigitis="' significantdigits +(-1) '"';
        if displayformat ne '' then
          put @7 'def:DisplayFormat="' displayformat +(-1) '"';
        if computationmethodoid ne '' then
          put @7 'def:ComputationMethodOID="' computationmethodoid +(-1) '"';
        put %if "&standard" = "SDTM" %then
            @7 'Origin="' origin +(-1) '"' / ;
              @7 'Comment="' comment +(-1) '"' /
            @7 'def:Label="' label +(-1) '">';
 
        if codelistname ne '' then
          put @7 '<CodeListRef CodeListOID="CodeList.' codelistname +(-1) '"/>';
 
        if valuelistoid ne '' then
          put @7 '<def:ValueListRef ValueListOID="' valuelistoid +(-1) '"/>';
 
        put @5 '</ItemDef>';
    run;
 
 
    **** ADD ITEMDEFS FOR VALUE LEVEL ITEMS TO "ITEMDEF" SECTION;
    filename idefvl "&out.itemdef_value.xml";
 
    data itemdefvalue;
        set valuelevel end=eof;
        by valuelistoid;
 
        file idefvl notitles;
 
        if _n_ = 1 then
          put @5 "<!-- ************************************************************ -->" /
              @5 "<!-- The details of value level items are here                    -->" /
              @5 "<!-- ************************************************************ -->" ;
 
        put @5 '<ItemDef OID="' itemoid  +(-1) '"' /
            @7 'Name="' valuename +(-1) '"' /
            @7 'DataType="' type +(-1) '"' /
            @7 'Length="' length +(-1) '"';
        if significantdigits ne '' then
          put @7 'SignificantDigitis="' significantdigits +(-1) '"';
        if displayformat ne '' then
          put @7 'def:DisplayFormat="' displayformat +(-1) '"';
        if computationmethodoid ne '' then
          put @7 'def:ComputationMethodOID="' computationmethodoid +(-1) '"';
        put %if "&standard" = "SDTM" %then
            @7 'Origin="' origin +(-1) '"' / ;
            @7 'Comment="' comment +(-1) '"' /
            @7 'def:Label="' label +(-1) '">';
 
        if codelistname ne '' then
          put @7 '<CodeListRef CodeListOID="CodeList.' codelistname +(-1) '"/>';
 
        put @5 '</ItemDef>';
    run;
 
 
    **** ADD ANALYSIS RESULTS METADATA SECTION FOR ADAM;
    %if "&standard" = "ADAM" %then
      %do;
        filename ar "&out.analysisresults.xml";
 
        data _null_;
          set analysisresults;
          ** note that it is required that identical display IDs be adjacent to
            ** each other in the metadata spreadsheet;
          by displayid notsorted;
 
          file ar notitles;
          if _n_ = 1 then
            put @5 "<!-- ************************************************************ -->" /
                @5 "<!-- Analysis Results MetaData are Presented Below                -->" /
                @5 "<!-- ************************************************************ -->"
                ;
          if first.displayid then
            put @5 '<adamref:AnalysisResultDisplays>' /
                @7 '<adamref:ResultDisplay DisplayIdentifier="' displayid +(-1)
                   '" OID="' displayid +(-1) '" DisplayLabel="' displayname +(-1)
                   '" leafID="' displayid +(-1) '">'  ;
 
          put @9 '<adamref:AnalysisResults ' /
              @9 'OID="' arid +(-1) '"' /
              @9 'ResultIdentifier="' resultid +(-1) '"' /
              @9 'Reason="' reason +(-1) '">' /
              @9 '<!-- List the parameters and parameter codes -->' /
              @9 '<adamref:ParameterList>'
              ;
 
          ** loop through PARAMCD/PARAM sets;
          set = 1;
          do while(scan(paramlist,set,'|') ne '');
            paramset = scan(paramlist,set,'|');
            paramcd  = scan(paramset,1,'/\');
            param    = trim(scan(paramset,2,'/\'));
            put @11 '<adamref:Parameter ParamCD="' paramcd +(-1)
                    '" Param="' param +(-1) '"/>' ;
            set = set + 1;
          end;
          put @9 '</adamref:ParameterList>';
 
          ** loop through the analysis variables;
          set = 1;
          do while(scan(analysisvariables,set,',') ne '');
            analysisvar = scan(analysisvariables,set,',');
            put @11 '<adamref:AnalysisVariable ItemOID="' analysisdataset +(-1)
                    '.' analysisvar +(-1) '"/>';
            set = set + 1;
          end;
    put @9 '<!-- AnalysisDatasets are  pairs of dataset references and selection criteria. Dataset references are ItemGroupRefs.  The label in the xsl is the def:label for the ItemGroup -
          put @9 '<adamref:AnalysisDataset>' /
              @11  '<ItemGroupRef ItemGroupOID="' analysisdataset +(-1) '" Mandatory="No"/>' /
              @11  '<adamref:SelectionCriteria>' /
              @13    '<def:ComputationMethod OID="SC' _n_ z3.
                     '" Name="Selection Criteria ' _n_ z3. '"> [' selectioncriteria ' ]</def:ComputationMethod> '/
              @11  '</adamref:SelectionCriteria> ' /
              @9 '</adamref:AnalysisDataset> ' /
              @9 '<adamref:Documentation leafID="' docleafid +(-1) '">' /
              @11  '<TranslatedText xml:lang="en">' Documentation  /
              @11  '</TranslatedText>'        /
              @9  '</adamref:Documentation>'  /
              @9  '<adamref:ProgrammingCode>' /
              @9  '<def:ComputationMethod OID="'  displayid +(-1) '">' /
              @1  ProgrammingCode /
              @9 '</def:ComputationMethod>' /
              @9 '</adamref:ProgrammingCode>' /
              @7 '</adamref:AnalysisResults>' ;
 
          if last.displayid then
            put @5 '</adamref:ResultDisplay>' /
                @5 '</adamref:AnalysisResultDisplays>'
                ;
        run;
      %end;
 
 
    **** CREATE CODELIST SECTION;
    filename codes "&xml.codelist.xml";
 
    proc sort
        data=codelists
        nodupkey;
        by codelistname codedvalue translated;
    run;
 
    **** MAKE SURE CODELIST IS UNIQUE;
    data _null_;
        set codelists;
        by codelistname codedvalue;
 
        if not (first.codedvalue and last.codedvalue) then
          put "ERR" "OR: multiple versions of the same coded value "
               codelistname= codedvalue=;
    run;
 
    proc sort
        data=codelists;
        by codelistname rank;
    run;
 
    data codelists;
        set codelists end=eof;
        by codelistname rank;
 
        file codes notitles;
 
        if _n_ = 1 then
          put @5 "<!-- ************************************************************ -->" /
              @5 "<!-- Codelists are presented below                                -->" /
              @5 "<!-- ************************************************************ -->" ;
 
        if first.codelistname then
          put @5 '<CodeList OID="CodeList.' codelistname +(-1) '"' /
              @7 'Name="' codelistname +(-1) '"' /
              @7 'DataType="' type +(-1) '">';
 
        **** output codelists that are not external dictionaries;
        if codelistdictionary = '' then
            do;
            put @7  '<CodeListItem CodedValue="' codedvalue +(-1) '"' @;
            if rank ne . then
                put ' def:Rank="' rank +(-1) '">';
              else
                put '>';
            put @9  '<Decode>' /
                @11 '<TranslatedText>' translated +(-1) '</TranslatedText>' /
                @9  '</Decode>' /
                @7  '</CodeListItem>';
          end;
        **** output codelists that are pointers to external codelists;
        if codelistdictionary ne '' then
          put @7 '<ExternalCodeList Dictionary="' codelistdictionary +(-1)
                 '" Version="' codelistversion +(-1) '"/>';
 
        if last.codelistname then
          put @5 '</CodeList>';
 
        if eof then
          put @3 '</MetaDataVersion>' /
              @1 '</Study>' /
              @1 '</ODM>';
    run;
 
    %mend cln_100makedefine;
    ;;;;
    run;quit;
 
    * put the xml pieces together;
 
    * ADAM XML;
 
    %cln_100makedefine(path=&gbl_root/xls/,metadata=cln_adammetadata.xlsx,out=&gbl_root/xml/cln_adam);
 
    filename def  (
           "d:/cln/xml/cln_adamanalysisresults.xml"
           "d:/cln/xml/cln_adamcompmethod.xml"
           "d:/cln/xml/cln_adamdefine_header.xml"
           "d:/cln/xml/cln_adamitemdef.xml"
           "d:/cln/xml/cln_adamitemdef_value.xml"
           "d:/cln/xml/cln_adamitemgroupdef.xml"
           "d:/cln/xml/cln_adamleaves.xml"
           "d:/cln/xml/cln_adamvaluelist.xml"
          );
    data _null_;
 
       infile def lrecl=384 recfm=v;
       file "d:/cln/xml/cln_adamDefine.xml";
       input;
       put _infile_;
 
    run;quit;
 
    * SDTM XML;
 
 
    %cln_100makedefine(path=&gbl_root/xls/,metadata=cln_sdtmmetadata.xlsx,out=&gbl_root/xml/cln_sdtm);
 
    filename def  (
           "d:/cln/xml/cln_sdtmcompmethod.xml"
           "d:/cln/xml/cln_sdtmdefine_header.xml"
           "d:/cln/xml/cln_sdtmitemdef.xml"
           "d:/cln/xml/cln_sdtmitemdef_value.xml"
           "d:/cln/xml/cln_sdtmitemgroupdef.xml"
           "d:/cln/xml/cln_sdtmvaluelist.xml"
          );
    data _null_;
 
       infile def lrecl=384 recfm=v;
       file "d:/cln/xml/cln_sdtmDefine.xml";
       input;
       put _infile_;
 
    run;quit;
 
    /*
 
    CURRENT STATE OF DIRECTORIES
 
    Directory of &gbl_root/cln
 
    - added files
 
        cln_fmt.sas7bcat
                                         * formats
       <DIR>  b64
                  cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                  cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
 
       <DIR>  xls
                  cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                  cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
 
                - cln_adamDefine.xml           * concatenation of all odm sections below
 
                - cln_adamdefine_header.xml    * excel odm section created from excel tab header
                - cln_adamanalysisresults.xml  ..
                - cln_adamcompmethod.xml
                - cln_adamitemdef.xml
                - cln_adamitemdef_value.xml
                - cln_adamitemgroupdef.xml
                - cln_adamleaves.xml
                - cln_adamvaluelist.xml
                - cln_sdtmcompmethod.xml
 
                - cln_sdtmDefine.xml         * concatenation of all stdm sections below
 
                - cln_sdtmdefine_header.xml  * excel odm section created from excel tab header
                - cln_sdtmitemdef.xml        ..
                - cln_sdtmitemdef_value.xml
                - cln_sdtmitemgroupdef.xml
                - cln_sdtmvaluelist.xml
    */
 
 
    /*              _       _       __ _                             _
      ___ _ __   __| |   __| | ___ / _(_)_ __   ___  __  ___ __ ___ | |
     / _ \ `_ \ / _` |  / _` |/ _ \ |_| | `_ \ / _ \ \ \/ / `_ ` _ \| |
    |  __/ | | | (_| | | (_| |  __/  _| | | | |  __/  >  <| | | | | | |
     \___|_| |_|\__,_|  \__,_|\___|_| |_|_| |_|\___| /_/\_\_| |_| |_|_|
 
 
             _ _
      ___ __| (_)___  ___   _ __ ___   __ _  ___ _ __ ___  ___
     / __/ _` | / __|/ __| | `_ ` _ \ / _` |/ __| `__/ _ \/ __|
    | (_| (_| | \__ \ (__  | | | | | | (_| | (__| | | (_) \__ \
     \___\__,_|_|___/\___| |_| |_| |_|\__,_|\___|_|  \___/|___/
     ___  ___            _        ____      _ _             ____
    / / |/ _ \ _ __ ___ | | _____/ ___|  __| | |_ _ __ ___ |  _ \ _   _
    | | | | | | `_ ` _ \| |/ / _ \___ \ / _` | __| `_ ` _ \| | | | | | |
    | | | |_| | | | | | |   <  __/___) | (_| | |_| | | | | | |_| | |_| |
    |_|_|\___/|_| |_| |_|_|\_\___|____/ \__,_|\__|_| |_| |_|____/ \__, |
                                                                  |___/
    */
 
    filename ft15f001 "&gbl_root/oto/cln_110mkesdtmdy.sas";
    parmcards4;
    *---------------------------------------------------------------*;
    * make_sdtm_dy.sas is a SAS macro that takes two SDTM --DTC dates
    * and calculates a SDTM study day (--DY) variable. It must be used
    * in a datastep that has both the REFDATE and DATE variables
    * specified in the macro parameters below.
    * MACRO PARAMETERS:
    * refdate = --DTC baseline date to calculate the --DY from.
    *           Generally RFSTDTC.
    * date = --DTC date to calculate the --DY to.  The variable
    *          associated with the --DY variable.
    *---------------------------------------------------------------*;
    %macro cln_110mkesdtmdy(refdate=,date=);
 
        if length(&date) >= 10 and length(&refdate) >= 10 then
          do;
            if input(&date,yymmdd10.) >=
               input(&refdate,yymmdd10.) then
              %upcase(%substr(&date,1,%length(&date)-3))DY = input(&date,yymmdd10.)- input(&refdate,yymmdd10.)+ 1;
            else
              %upcase(%substr(&date,1,%length(&date)-3))DY = input(&date,yymmdd10.)- input(&refdate,yymmdd10.);
          end;
 
    %mend cln_110mkesdtmdy;
    ;;;;
    run;quit;
 
 
    /* ____   ___            _                  _            _
    / |___ \ / _ \ _ __ ___ | | _____  ___ _ __| |_ ___   __| |_ __
    | | __) | | | | `_ ` _ \| |/ / _ \/ __| `__| __/ _ \ / _` | `__|
    | |/ __/| |_| | | | | | |   <  __/\__ \ |  | || (_) | (_| | |
    |_|_____|\___/|_| |_| |_|_|\_\___||___/_|   \__\___/ \__,_|_|
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_120mkesrtodr.sas";
    parmcards4;
    *----------------------------------------------------------------*;
    * make_sort_order.sas creates a global macro variable called
    * **SORTSTRING where ** is the name of the dataset that contains
    * the metadata specified sort order for a given dataset.
    *
    * MACRO PARAMETERS:
    * metadatafile = the file containing the dataset metadata
    * dataset = the dataset or domain name
    *----------------------------------------------------------------*;
    %macro cln_120mkesrtodr(metadatafile=,dataset=);
 
        proc import
            datafile="&metadatafile"
            out=_temp
            dbms=xlsx
            replace;
            sheet="TOC_METADATA";
        run;
 
        ** create **SORTSTRING macro variable;
        %global &dataset.SORTSTRING;
        data _null_;
          set _temp;
 
            where name = "&dataset";
 
            call symputx(compress("&dataset" || "SORTSTRING"),
                         translate(domainkeys," ",","));
        run;
 
    %mend cln_120mkesrtodr;
    ;;;;
    run;quit;
 
 
    /* _____  ___        __ _
    / |___ / / _ \  ___ / _| |__
    | | |_ \| | | |/ __| |_| `_ \
    | |___) | |_| | (__|  _| |_) |
    |_|____/ \___/ \___|_| |_.__/
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_130cfb.sas";
    parmcards4;
    *---------------------------------------------------------;
    * Macro for deriving ABLFL, BASE, CHG, and PCHG for a BDS
    *   formatted ADaM data set;
    * Assumes baseline is the last non-missing value on or before
    *   study day 1 and that the INDDATA is an SDTM data set with
    *   variables USUBJID and VISITNUM
    *---------------------------------------------------------;
    %macro cln_130cfb(indata= ,outdata= ,avalvar= ,dayvar= ,keepvars= );
 
        proc sort
          data = &indata
          out = &outdata (rename = (&avalvar = aval));
            by usubjid visitnum;
        run;
 
        * Baseline is defined as the last non-missing value prior to study day 1 first dose;
        * (note, values on Day 1 are assumed to occur before the first dose);
        data base1 (keep = usubjid visitnum) base2 (keep = usubjid base);
          set &outdata;
            where &dayvar<=1 and aval > .z;
            by usubjid visitnum;
 
            rename aval = base;
            if last.usubjid;
        run;
 
        * Do one merge to identify the baseline record;
        data &outdata;
          merge &outdata base1 (in = inbase);
            by usubjid visitnum;
 
               if inbase then
                 ablfl = 'Y';
        run;
 
        * Do another merge to merge in the baseline value;
        data &outdata;
          merge &outdata base2;
            by usubjid;
 
               %if &keepvars^= %then
                 keep  &keepvars;
               ;
 
               chg  = aval - base;
               pchg = divide(chg,base*100);
        run;
 
    %mend cln_130cfb;
    ;;;;
    run;quit;
 
    /* _  _    ___            _        __  __ _        _____ _     _
    / | || |  / _ \ _ __ ___ | | _____|  \/  | |_ _   |_   _| |__ | |
    | | || |_| | | | `_ ` _ \| |/ / _ \ |\/| | __| | | || | | `_ \| |
    | |__   _| |_| | | | | | |   <  __/ |  | | |_| |_| || | | |_) | |
    |_|  |_|  \___/|_| |_| |_|_|\_\___|_|  |_|\__|\__, ||_| |_.__/|_|
                                                  |___/
    */
    filename ft15f001 "&gbl_root/oto/cln_140mkeMtyTbl.sas";
    parmcards4;
    *---------------------------------------------------------------*;
    * make_empty_dataset.sas creates a zero record dataset based on a
    * dataset metadata spreadsheet.  The dataset created is called
    * EMPTY_** where "**" is the name of the dataset.  This macro also
    * creates a global macro variable called **KEEPSTRING that holds
    * the dataset variables desired and listed in the order they
    * should appear.  [The variable order is dictated by VARNUM in the
    * metadata spreadsheet.]
    *
    * MACRO PARAMETERS:
    * metadatafile = the MS Excel file containing the dataset metadata
    * dataset = the dataset or domain name you want to extract
    *---------------------------------------------------------------*;
    %macro cln_140mkeMtyTbl(metadatafile=,dataset=);
        /*
          %let metadatafile=&gbl_root.:/cln/xls/cln_sdtmMetadata.xlsx;
          %let dataset=DM;
        */
        %global keepstring;
        %local i;
        proc import
            datafile="&metadatafile"
            out=_temp
            dbms=xlsx
            replace;
            sheet="VARIABLE_METADATA";
        run;
 
        ** sort the dataset by expected specified variable order;
        proc sort
          data=_temp;
            where domain = "&dataset";
            by varnum;
        run;
 
        ** create keepstring macro variable and load metadata
        ** information into macro variables;
        %global &dataset.KEEPSTRING;
        data _null_;
          set _temp nobs=nobs end=eof;
 
            if _n_=1 then
              call symput("vars", compress(put(nobs,3.)));
 
            call symputx('var'    || compress(put(_n_, 3.)), variable);
            call symputx('label'  || compress(put(_n_, 3.)), label);
            call symputx('length' || compress(put(_n_, 3.)), put(length, 3.));
 
            ** valid ODM types include TEXT, INTEGER, FLOAT, DATETIME,
            ** DATE, TIME and map to SAS numeric or character;
            if upcase(type) in ("INTEGER", "FLOAT") then
              call symputx('type' || compress(put(_n_, 3.)), "");
            else if upcase(type) in ("TEXT", "DATE", "DATETIME",
                                     "DATE", "TIME") then
              call symputx('type' || compress(put(_n_, 3.)), "$");
            else
              put "ERR" "OR: not using a valid ODM type.  " type=;
 
 
            ** create **KEEPSTRING macro variable;
            length keepstring $ 32767;
            retain keepstring;
            keepstring = compress(keepstring) || "|" || left(variable);
            if eof then
              call symputx(upcase(compress("&dataset" || 'KEEPSTRING')),
                           left(trim(translate(keepstring," ","|"))));
        run;
 
 
        ** create a 0-observation template data set used for assigning
        ** variable attributes to the actual data sets;
        data EMPTY_&dataset;
            %do i=1 %to &vars;
               attrib &&var&i label="&&label&i" length=&&type&i.&&length&i...;
               %if &&type&i=$ %then
                 retain &&var&i '';
               %else
                 retain &&var&i .;
                ;
            %end;
            if 0;
        run;
 
    %mend cln_140mkeMtyTbl;
    ;;;;
    run;quit;
 
    /* ____   ___      _                       _
    / | ___| / _ \  __| | ___  _ __ ___   __ _(_)_ __ __  __
    | |___ \| | | |/ _` |/ _ \| `_ ` _ \ / _` | | `_ \\ \/ /
    | |___) | |_| | (_| | (_) | | | | | | (_| | | | | |>  <
    |_|____/ \___/ \__,_|\___/|_| |_| |_|\__,_|_|_| |_/_/\_\
 
    */
    filename ft15f001 "&gbl_root/oto/cln_150domainx.sas";
    parmcards4;
    %macro cln_150domainx(domain= ,suppqual=0);
 
        /*
          %let domain=DM;
          %let subqual=0;
          %let outlib=work;
        */
 
        %local suppdata idvar varlist nvars;
 
        %if &suppqual %then
          %let suppdata=suppqual;
        %else
          %let suppdata=supp&domain;
        ;
 
        %* count the number of supplemental qualifiers for the given domain;
         proc sort
          data = &sourcelib..&suppdata
          out = nvars
          nodupkey;
            where rdomain=upcase("&domain");
            by qnam idvar;
        run;
 
        /*
        Up to 40 obs WORK.NVARS total obs=2
 
         STUDYID    RDOMAIN    USUBJID    IDVAR    IDVARVAL     QNAM      QLABEL                   QVAL       QORIG    QEVAL
 
         XYZ123       DM       UNI101                          RACEOTH    Race, Other           BRAZILIA       CRF
         XYZ123       DM       UNI101                          RANDDTC    Randomization Date    2010-04-02     CRF
        */
 
        data _null_;
          set nvars end=eof;
            by qnam idvar;
               length varlist $200;
               retain varlist;
               if not first.qnam then
                  put 'PROB' 'LEM: More than one IDVAR for the domain-- '
                       rdomain= qnam=
                                idvar= ;
               else
                  do;
                     nvars + 1;
                     varlist = trim(varlist) || " " || trim(qnam);
                  end;
               if eof then
                 do;
                    call symput("nvars", put(nvars, 2.));
                    call symput("varlist", trim(left(varlist)));
                    call symput("idvar", trim(idvar));
                 end;
        run;
 
        %put domain=&domain idvar=&idvar nvars=&nvars varlist=&varlist;
 
        proc sort
          data = &sourcelib..&suppdata
          out = supp&domain;
            where rdomain=upcase("&domain");
            by usubjid idvar idvarval;
        run;
 
        /*
        Up to 40 obs WORK.SUPPDM total obs=66
 
        Obs    STUDYID    RDOMAIN    USUBJID    IDVAR    IDVARVAL     QNAM      QLABEL                QVAL          QORIG    QEVAL
 
          1    XYZ123       DM       UNI101                          RACEOTH    Race, Other           BRAZILIA       CRF
          2    XYZ123       DM       UNI101                          RANDDTC    Randomization Date    2010-04-02     CRF
          3    XYZ123       DM       UNI102                          RANDDTC    Randomization Date    2010-02-13     CRF
         */
 
        %*  determine whether IDVAR in the parent domain is character or numeric;
 
        %if &idvar^= %then
          %do;
            %let dsetnum=%sysfunc(open(&sourcelib..&domain));
            %let varnum=%sysfunc(varnum(&dsetnum,&idvar));
            %let idtype=%sysfunc(vartype(&dsetnum,&varnum));
            %let rc=%sysfunc(close(&dsetnum));
          %end;
        %else
          %let idtype= ;
 
        data supp&domain.x;
          set supp&domain;
            by usubjid idvar idvarval;
               drop q: idvarval idvar i rdomain;
               length &varlist $200.;
               retain &varlist;
               array vars{*} &varlist;
               if first.idvarval then
                 do i = 1 to dim(vars);
                   vars{i} = '';
                 end;
               do i = 1 to dim(vars);
                 if upcase(qnam)=upcase(vname(vars{i})) then
                   vars{i} = qval;
               end;
             /*
               %** convert to numeric if numeric in the parent domain;
               %if &idvar^= and &idtype=N %then
                  &idvar = input(idvarval, best.);
               %else %if &idvar^= %then
                 &idvar = idvarval;
               ;
              */
               if last.idvarval;
        run;
 
        proc sort
          data = supp&domain.x;
            by usubjid &idvar;
        run;quit;
 
        proc sort
          data = &sourcelib..&domain
          out = __tmp;
            by usubjid &idvar;
        run;quit;
 
        data &outlib..&domain;
          merge __tmp supp&domain ;
            by usubjid &idvar;
        run;
 
        /*
        Middle Observation(33 ) of work.DM - Total Obs 66
 
 
         -- CHARACTER --
        STUDYID             C15      XYZ123              Study Identifier
        DOMAIN              C2       DM                  Domain Abbreviation
        USUBJID             C25      UNI504              Unique Subject Identifier
        SUBJID              C7       504                 Subject Identifier for the Study
        RFSTDTC             C16      2010-06-19          Subject Reference Start Date/Time
        RFENDTC             C16      2010-12-20          Subject Reference End Date/Time
        SITEID              C7       500                 Study Site Identifier
        BRTHDTC             C16      1977-02-01          Date/Time of Birth
        AGEU                C5       YEARS               Age Units
        SEX                 C2       Wh                  Sex
        RACE                C40      Other               Race
        ARMCD               C8         0                 Planned Arm Code
        ARM                 C40      Placebo             Description of Planned Arm
        COUNTRY             C3       USA                 Country
        RDOMAIN             C2       DM                  Related Domain Abbreviation
        IDVAR               C8                           Identifying Variable
        IDVARVAL            C200                         Identifying Variable Value
        QNAM                C8       RANDDTC             Qualifier Variable Name
        QLABEL              C40      Randomization Da    Qualifier Variable Label
        QVAL                C200     2010-06-19          Data Value
        QORIG               C8       CRF                 Origin
        QEVAL               C8                           Evaluator
        TOTOBS              C16      66                  TOTOBS
 
 
         -- NUMERIC --
        AGE                 N8       33                  Age
        */
 
      %mend cln_150domainx;
    ;;;;
    run;quit;
 
    /*  __    ___      _ _       ____     _ _
    / |/ /_  / _ \  __| | |_ ___|___ \ __| | |_
    | | `_ \| | | |/ _` | __/ __| __) / _` | __|
    | | (_) | |_| | (_| | || (__ / __/ (_| | |_
    |_|\___/ \___/ \__,_|\__\___|_____\__,_|\__|
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_160dtc2dt.sas";
    parmcards4;
    %macro cln_160dtc2dt (dtcvar , prefix=a, refdt= );
 
        if length(trim(&dtcvar))=10 and index(&dtcvar,'--')=0 then
          &prefix.dt = input(&dtcvar, yymmdd10.);
        else if length(&dtcvar)=16 and index(&dtcvar,'--')=0 and index(&dtcvar,'-:')=0 then
          do;
            &prefix.dtm = input(trim(&dtcvar)||":00", e8601dt19.);
            &prefix.dt  = datepart(&prefix.dtm);
            * optionally add formats: ;
            * format &prefix.dtm datetime16.;
          end;
 
        %if &refdt^= %then
          %do;
            if .<&prefix.dt<&refdt then
              &prefix.dy = &prefix.dt - &refdt;
            else if &prefix.dt>=&refdt then
              &prefix.dy = &prefix.dt - &refdt + 1;
          %end;
        * optionally add formats: ;
        * format &prefix.dt yymmdd10. ;
 
    %mend cln_160dtc2dt;
    ;;;;
    run;quit;
 
    /* _____ ___
    / |___  / _ \ _ __ ___  _ __ __ _ ___ _   _ _ __
    | |  / / | | | `_ ` _ \| `__/ _` / __| | | | `_ \
    | | / /| |_| | | | | | | | | (_| \__ \ |_| | |_) |
    |_|/_/  \___/|_| |_| |_|_|  \__, |___/\__,_| .__/
                                |___/          |_|
    */
 
    filename ft15f001 "&gbl_root/oto/cln_170mrgsup.sas";
    parmcards4;
    *------------------------------------------------------------;
    * Merge supplemental qualifiers into the parent SDTM domain;
    * This can either be for an entire library or for specified
    * domains;
    *------------------------------------------------------------;
 
    %macro cln_170mrgsup(sourcelib=library, outlib=WORK, domains= , suppqual=0);
      %local domain;
 
         /*
          libname cln "d:/cln";
          %let sourcelib=cln;
          %let domains=DM;
          %let domain=DM;
         */
 
      %** de-normalize suppqual and merge into the given domain;
 
        %*-------------------------------------------------------;
        %* If DOMAINS parameter specified, then loop through those
        %* domains;
        %* otherwise, dynamically identify the SUPPxx data sets and
        %* go through them all;
        %*-------------------------------------------------------;
        %let _wrd=1;
        %if &DOMAINS^= %then
          %do %while(%scan(&domains,&_wrd)^= );
              %let domain=%scan(&domains,&_wrd);
 
              %cln_150domainx(domain=&domain,suppqual=0);
 
              %let _wrd=%eval(&_wrd+1);
          %end;
        %else
          %do;
              %** find all of the SUPPxx datasets and loop through each one;
              ods output members=members;
              proc contents
                    data = &sourcelib.._all_ memtype=data nods ;
              run;
             data membersx;
                set members;
                 if upcase(name)=:'SUPP' and upcase(name)^=:'SUPPQUAL' then
                          do;
                          rdomain = substr(name,5,2);
                          put name= rdomain= ;
 
                         output;
                       end;
                     else if upcase(name)=:'SUPPQUAL' then
                       call symput("suppqual","1");
              run;
 
              %** loop through each domain;
              proc sql /* noprint */;
               select count(distinct rdomain)
                 into :domn
                 from %if &suppqual %then &sourcelib..suppqual; %else
                    membersx;
               quit;
               proc sql;
               select distinct rdomain
                 into :domain1 - :domain%left(&domn)
                 from %if &suppqual %then &sourcelib..suppqual; %else
                    membersx;
                 ;
              %do _i=1 %to &domn;
                %cln_150domainx(domain=&&domain&_i,suppqual=&suppqual);
              %end;
          %end; %* if domains not specified explicitly...;
 
    %mend cln_170mrgsup;
    ;;;;
    run;quit;
 
    /*
 
    CURRENT STATE OF DIRECTORIES
 
    - added files
 
    Directory of &gbl_root/cln
 
        cln_fmt.sas7bcat
                                         * formats
       <DIR>  b64
                  cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                  cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
                  cln_010.sas            *  SAS Tools
 
                - cln_100makedefine.sas  * CDISC utilities ( extract meta data from excel tabs;
                - cln_110mkesdtmdy.sas
                - cln_120mkesrtodr.sas
                - cln_130cfb.sas
                - cln_140mkeMtyTbl.sas
                - cln_150domainx.sas
                - cln_160dtc2dt.sas
                - cln_170mrgsup.sas
       <DIR>  xls
                  cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                  cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
 
                  cln_adamDefine.xml           * concatenation of all odm sections below
 
                  cln_adamdefine_header.xml    * excel odm section created from excel tab header
                  cln_adamanalysisresults.xml  ..
                  cln_adamcompmethod.xml
                  cln_adamitemdef.xml
                  cln_adamitemdef_value.xml
                  cln_adamitemgroupdef.xml
                  cln_adamleaves.xml
                  cln_adamvaluelist.xml
                  cln_sdtmcompmethod.xml
 
                  cln_sdtmDefine.xml         * concatenation of all stdm sections below
 
                  cln_sdtmdefine_header.xml  * excel odm section created from excel tab header
                  cln_sdtmitemdef.xml        ..
                  cln_sdtmitemdef_value.xml
                  cln_sdtmitemgroupdef.xml
                  cln_sdtmvaluelist.xml
       <DIR>  xpt
       <DIR>  zip
    */
 
 
    /*              _           _ _
      ___ _ __   __| |   ___ __| (_)___  ___   _ __ ___   __ _  ___ _ __ ___  ___
     / _ \ `_ \ / _` |  / __/ _` | / __|/ __| | `_ ` _ \ / _` |/ __| `__/ _ \/ __|
    |  __/ | | | (_| | | (_| (_| | \__ \ (__  | | | | | | (_| | (__| | | (_) \__ \
     \___|_| |_|\__,_|  \___\__,_|_|___/\___| |_| |_| |_|\__,_|\___|_|  \___/|___/
 
                _                              _       _                 _
      __ _  ___| |_   _ __ __ ___      __   __| | __ _| |_ __ _ ___  ___| |_ ___
     / _` |/ _ \ __| | `__/ _` \ \ /\ / /  / _` |/ _` | __/ _` / __|/ _ \ __/ __|
    | (_| |  __/ |_  | | | (_| |\ V  V /  | (_| | (_| | || (_| \__ \  __/ |_\__ \
     \__, |\___|\__| |_|  \__,_| \_/\_/    \__,_|\__,_|\__\__,_|___/\___|\__|___/
     |___/
 
      you can download the individual raw sas datases into the folder below or run this script
 
         ./cln/cln_rawadverse.sas7bdat
         ./cln/cln_rawdemographic.sas7bdat
         ./cln/cln_rawdosing.sas7bdat
         ./cln/cln_rawlabs.sas7bdat
         ./cln/cln_rawpain.sas7bdat
 
       There are warning whem trying to clear a file when it is not defined. This is a precaution because in any section of code fails
       a difficult conflict may arise and you may not be able to use the fileref.
 
    */
 
       filename download clear;
       filename download "&gbl_root/b64/cln_rawdemographic.b64";
       %*utl_b64encode(&gbl_root/sd1/cln_rawdemographic.sas7bdat,&gbl_root/b64/cln_rawdemographic.b64); /*only if I need to upload another newer version of the data */
       proc http
          method='GET'
          url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_rawdemographic.b64"
          out=download;
       run;quit;
       filename download clear;
       %utl_b64decode(&gbl_root/b64/cln_rawdemographic.b64,&gbl_root/cln_rawdemographic.sas7bdat);
 
       filename download clear;
       filename download "&gbl_root/b64/cln_rawadverse.b64";
       %*utl_b64encode(&gbl_root/sd1/cln_rawadverse.sas7bdat,&gbl_root/b64/cln_rawadverse.b64);/* not needed */
       proc http
          method='GET'
          url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_rawadverse.b64"
          out=download;
       run;quit;
       filename download clear;
       %utl_b64decode(&gbl_root/b64/cln_rawadverse.b64,&gbl_root/cln_rawadverse.sas7bdat);
 
 
       filename download clear;
       filename download "&gbl_root/b64/cln_rawdosing.b64";
       %*utl_b64encode(&gbl_root/sd1/cln_rawdosing.sas7bdat,&gbl_root/b64/cln_rawdosing.b64);/* not needed */
       proc http
          method='GET'
          url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_rawdosing.b64"
          out=download;
       run;quit;
       filename download clear;
       %utl_b64decode(&gbl_root/b64/cln_rawdosing.b64,&gbl_root/cln_rawdosing.sas7bdat);
 
       filename download clear;
       filename download "&gbl_root/b64/cln_rawlabs.b64";
       %*utl_b64encode(&gbl_root/sd1/cln_rawlabs.sas7bdat,&gbl_root/b64/cln_rawlabs.b64);/* not needed */
       proc http
          method='GET'
             url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_rawlabs.b64"
          out=download;
       run;quit;
       filename download clear;
       %utl_b64decode(&gbl_root/b64/cln_rawlabs.b64,&gbl_root/cln_rawlabs.sas7bdat);
 
       filename download clear;
       filename download "&gbl_root/b64/cln_rawpain.b64";
       %*utl_b64encode(&gbl_root/sd1/cln_rawpain.sas7bdat,&gbl_root/b64/cln_rawpain.b64);  /* not needed */
       proc http
          method='GET'
             url="https://raw.githubusercontent.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing/master/cln_rawpain.b64"
          out=download;
       run;quit;
       filename download clear;
       %utl_b64decode(&gbl_root/b64/cln_rawpain.b64,&gbl_root/cln_rawpain.sas7bdat);
 
     /*
 
    /*
 
    CURRENT STATE OF DIRECTORIES
 
    - added files
 
    Directory of &gbl_root/cln
 
        cln_fmt.sas7bcat            * formats
 
      - cln_rawadverse.sas7bdat     * raw data
      - cln_rawdemographic.sas7bdat
      - cln_rawdosing.sas7bdat
      - cln_rawlabs.sas7bdat
      - cln_rawpain.sas7bdat
                                         * formats
       <DIR>  b64
                  cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                  cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
 
                - cln_rawadverse.b64     * base 64 encoded raw sas datasets;
                - cln_rawdemographic.b64
                - cln_rawdosing.b64
                - cln_rawlabs.b64
                - cln_rawpain.b64
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
                  cln_010.sas            *  SAS Tools
 
                  cln_100makedefine.sas  * CDISC utilities ( extract meta data from excel tabs;
                  cln_110mkesdtmdy.sas
                  cln_120mkesrtodr.sas
                  cln_130cfb.sas
                  cln_140mkeMtyTbl.sas
                  cln_150domainx.sas
                  cln_160dtc2dt.sas
                  cln_170mrgsup.sas
       <DIR>  xls
                  cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                  cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
 
                  cln_adamDefine.xml           * concatenation of all odm sections below
 
                  cln_adamdefine_header.xml    * excel odm section created from excel tab header
                  cln_adamanalysisresults.xml  ..
                  cln_adamcompmethod.xml
                  cln_adamitemdef.xml
                  cln_adamitemdef_value.xml
                  cln_adamitemgroupdef.xml
                  cln_adamleaves.xml
                  cln_adamvaluelist.xml
                  cln_sdtmcompmethod.xml
 
                  cln_sdtmDefine.xml         * concatenation of all stdm sections below
 
                  cln_sdtmdefine_header.xml  * excel odm section created from excel tab header
                  cln_sdtmitemdef.xml        ..
                  cln_sdtmitemdef_value.xml
                  cln_sdtmitemgroupdef.xml
                  cln_sdtmvaluelist.xml
       <DIR>  xpt
       <DIR>  zip
                    _              _                              _       _
      ___ _ __   __| |   __ _  ___| |_   _ __ __ ___      __   __| | __ _| |_ __ _
     / _ \ `_ \ / _` |  / _` |/ _ \ __| | `__/ _` \ \ /\ / /  / _` |/ _` | __/ _` |
    |  __/ | | | (_| | | (_| |  __/ |_  | | | (_| |\ V  V /  | (_| | (_| | || (_| |
     \___|_| |_|\__,_|  \__, |\___|\__| |_|  \__,_| \_/\_/    \__,_|\__,_|\__\__,_|
                        |___/
                    _            _
      ___ _ __   __| |  ___  ___| |_ _   _ _ __
     / _ \ `_ \ / _` | / __|/ _ \ __| | | | `_ \
    |  __/ | | | (_| | \__ \  __/ |_| |_| | |_) |
     \___|_| |_|\__,_| |___/\___|\__|\__,_| .__/
                                          |_|
    */
 
 
 
 
    /*   _             _             _ _
     ___| |_ __ _ _ __| |_    ___ __| (_)___  ___
    / __| __/ _` | `__| __|  / __/ _` | / __|/ __|
    \__ \ || (_| | |  | |_  | (_| (_| | \__ \ (__
    |___/\__\__,_|_|   \__|  \___\__,_|_|___/\___|
 
 
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */
 
 
    /*___  ____ _____ __  __
    / ___||  _ \_   _|  \/  |___
    \___ \| | | || | | |\/| / __|
     ___) | |_| || | | |  | \__ \
    |____/|____/ |_| |_|  |_|___/
      ___   ___   ___          _ _             ____
    | ___| / _ \ / _ \ ___  __| | |_ _ __ ___ |  _ \ _ __ ___
    |___ \| | | | | | / __|/ _` | __| `_ ` _ \| | | | `_ ` _ \
     ___) | |_| | |_| \__ \ (_| | |_| | | | | | |_| | | | | | |
    |____/ \___/ \___/|___/\__,_|\__|_| |_| |_|____/|_| |_| |_|
                     _          ____  __  __
     _ __ ___   __ _| | _____  |  _ \|  \/  |
    | `_ ` _ \ / _` | |/ / _ \ | | | | |\/| |
    | | | | | | (_| |   <  __/ | |_| | |  | |
    |_| |_| |_|\__,_|_|\_\___| |____/|_|  |_|
 
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_500sdtmDm.sas";
    parmcards4;
    %macro cln_500sdtmDm ;
        *---------------------------------------------------------------*;
        * STDM_DM.sas creates the SDTM DM and SUPPDM datasets and saves them
        * as permanent SAS datasets to the sdtm libref.
        *---------------------------------------------------------------*;
 
        libname cln "&gbl_root";
 
        **** GET FIRST AND LAST DOSE DATE FOR RFSTDTC AND RFENDTC;
        proc sort
          data=cln.cln_rawdosing(keep=subject startdt enddt)
          out=dosing;
            by subject startdt;
        run;
        /*
        DOSING total obs=84
 
        Obs    SUBJECT     STARTDT        ENDDT
 
          1      101      04/02/2010    07/26/2010  1st dose
          2      101      07/31/2010    10/10/2010  last dose
 
          3      102      02/13/2010    03/20/2010
          4      102      03/25/2010    08/10/2010
 
          5      103      05/16/2010    11/14/2010
 
          6      104      01/02/2010    01/10/2010
          7      104      01/15/2010    05/25/2010
        */
 
        **** FIRSTDOSE=FIRST DOSING AND LASTDOSE=LAST DOSING;
        data dosing1st;
          set dosing;
            by subject;
            format firstdose lastdose mmddyy10.;
            retain firstdose lastdose;
 
            if first.subject then
              do;
                firstdose = .;
                lastdose = .;
              end;
 
            firstdose = min(firstdose,startdt,enddt);
            lastdose = max(lastdose,startdt,enddt);
            drop startdt enddt;
            if last.subject;
        run;
 
        /*
        Unique Patent first and last dose
        DOSING1ST total obs=60
 
        Obs    SUBJECT    FIRSTDOSE      LASTDOSE
 
          1      101      04/02/2010    10/10/2010
          2      102      02/13/2010    08/10/2010
          3      103      05/16/2010    11/14/2010
          4      104      01/02/2010    07/04/2010
          5      105      04/20/2010    10/19/2010
          6      106      04/01/2010    10/10/2010
        */
 
        **** GET DEMOGRAPHICS DATA;
        proc sort
          data=cln.cln_rawdemographic
          out=demographic;
            by subject;
        run;
 
        * ADD FIRST AND LAST DOSE TO PATIENT DEMOGRAPHICS;
        data demog_dose;
          merge demographic
                dosing1st;
            by subject;
        run;
 
        /*
        Middle Observation(30 ) of demog_dose - Total Obs 60
 
 
         -- CHARACTER --
        ORACE                 C8                   Oher Race Specify
        UNIQUEID              C6       UNI506      Company Wide Subject ID
        GENDER                C5       White       GENDER
        RACE                  C5       White       RACE
        TRT1                  C7       Placebo     TRT1
 
 
         -- NUMERIC --
        SUBJECT               N8       506         Subject Number
        TRT                   N8       0           Treatment
        DOB                   N8       1664        Date of Birth
        RANDDT                N8       18282       Randomization Date
 
 
        FIRSTDOSE             N8       18282       FIRSTDOSE    *** Added to unique patient record
        LASTDOSE              N8       18463       LASTDOSE
        */
 
        **** CREATE EMPTY DM DATASET SHELL CALLED EMPTY_DM (MATCH FDA VARIABLES ;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=DM);
 
        **** DERIVE THE MAJORITY OF SDTM DM VARIABLES;
        data dm;
          set EMPTY_DM
            demog_dose;
            studyid = 'XYZ123';
            domain = 'DM';
            usubjid = left(uniqueid);
            subjid = put(subject,3.);
            rfstdtc = put(firstdose,yymmdd10.);
            rfendtc = put(lastdose,yymmdd10.);
            siteid = substr(subjid,1,1) || "00";
            brthdtc = put(dob,yymmdd10.);
            age = floor ((intck('month',dob,firstdose) -
                  (day(firstdose) < day(dob))) / 12);
            if age ne . then
                ageu = 'YEARS';
            country = "USA";
            sex=gender;
            arm=trt1;
            armcd=put(trt,3.);
            drop gender trt trt1;
        run;
 
        /*
        The CONTENTS Procedure
 
        WORK.EMPTY_DM                 Observations          0
 
                            Variables in Creation Order
 
         #    Variable    Type    Len    Label
 
         1    STUDYID     Char     15    Study Identifier
         2    DOMAIN      Char      2    Domain Abbreviation
         3    USUBJID     Char     25    Unique Subject Identifier
         4    SUBJID      Char      7    Subject Identifier for the Study
         5    RFSTDTC     Char     16    Subject Reference Start Date/Time
         6    RFENDTC     Char     16    Subject Reference End Date/Time
         7    SITEID      Char      7    Study Site Identifier
         8    BRTHDTC     Char     16    Date/Time of Birth
         9    AGE         Num       8    Age
        10    AGEU        Char      5    Age Units
        11    SEX         Char      2    Sex
        12    RACE        Char     40    Race
        13    ARMCD       Char      8    Planned Arm Code
        14    ARM         Char     40    Description of Planned Arm
        15    COUNTRY     Char      3    Country
        */
 
 
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=DM);
 
        %put &=dmkeepstring;
        %put &=dmsortstring;
 
        /* FDA variables to keep based on define xml (in excel)
        DMKEEPSTRING=STUDYID DOMAIN USUBJID SUBJID RFSTDTC RFENDTC SITEID BRTHDTC AGE AGEU SEX RACE ARMCD ARM COUNTRY
 
        DMSORTSTRING=STUDYID  USUBJID
        */
 
        * SORT AND KEEP;
 
        proc sort
          data=dm(keep = &DMKEEPSTRING)
          out=cln.dm;
            by &DMSORTSTRING;
        run;
 
        /*
        Final SDTN DM dataset
        Middle Observation(30 ) of cln.dm - Total Obs 60
 
 
         -- CHARACTER --
        STUDYID               C15      XYZ123              Study Identifier
        DOMAIN                C2       DM                  Domain Abbreviation
        USUBJID               C25      UNI506              Unique Subject Identifier
        SUBJID                C7       506                 Subject Identifier for the Study
        RFSTDTC               C16      2010-01-20          Subject Reference Start Date/Time
        RFENDTC               C16      2010-07-20          Subject Reference End Date/Time
        SITEID                C7       500                 Study Site Identifier
        BRTHDTC               C16      1964-07-22          Date/Time of Birth
        AGEU                  C5       YEARS               Age Units
        SEX    ** issue       C2       Wh                  Sex
        RACE                  C40      White               Race
        ARMCD                 C8       0                   Planned Arm Code
        ARM                   C40      Placebo             Description of Planned Arm
        COUNTRY               C3       USA                 Country
        TOTOBS                C16      60                  TOTOBS
 
 
         -- NUMERIC --
        AGE                   N8       45                  Age
        */
 
        * SUPLEMENTA DM DATA;
 
        **** CREATE EMPTY SUPPDM DATASET CALLED EMPTY_DM;
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=SUPPDM);
 
 
        /*
        We do use the excel mete (same as XML) to set correct variable names, types and lengths
 
        Middle Observation(33 ) of SUPPDM - Total Obs 66
 
 
         -- CHARACTER --
        STUDYID             C15      XYZ123              Study Identifier
        RDOMAIN             C2       DM                  Related Domain Abbreviation
        USUBJID             C25      UNI504              Unique Subject Identifier
        IDVAR               C8                           Identifying Variable
        IDVARVAL            C200                         Identifying Variable Value
        QNAM                C8       RANDDTC             Qualifier Variable Name
        QLABEL              C40      Randomization Da    Qualifier Variable Label
        QVAL                C200     2010-06-19          Data Value
        QORIG               C8       CRF                 Origin
        QEVAL               C8                           Evaluator
        TOTOBS              C16      66                  TOTOBS
        */
 
        %put &=SUPPDMKEEPSTRING;
 
        * SUPPDMKEEPSTRING = STUDYID RDOMAIN USUBJID IDVAR IDVARVAL QNAM QLABEL QVAL QORIG QEVAL;
 
        * FILL IN AND USE SUPPDMKEEPSTRING TO KEEP FDA VARIABLES FROM MACRO CLN_140MKEMTYTBL;
 
        data suppdm;
          set EMPTY_SUPPDM
              dm;
 
            keep &SUPPDMKEEPSTRING;
 
            **** OUTPUT OTHER RACE AS A SUPPDM VALUE;
            if orace ne '' then
              do;
                rdomain = 'DM';
                qnam = 'RACEOTH';
                qlabel = 'Race, Other';
                qval = left(orace);
                qorig = 'CRF';
                output;
              end;
 
            **** OUTPUT RANDOMIZATION DATE AS SUPPDM VALUE;
            if randdt ne . then
              do;
                rdomain = 'DM';
                qnam = 'RANDDTC';
                qlabel = 'Randomization Date';
                qval = left(put(randdt,yymmdd10.));
                qorig = 'CRF';
                output;
              end;
        run;
 
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=SUPPDM);
 
        %put &=SUPPDMSORTSTRING;
 
        * SUPPDMSORTSTRING = STUDYID  RDOMAIN  USUBJID  IDVAR  IDVARVAL  QNAM;
 
        proc sort
          data=suppdm
          out=cln.suppdm;
            by &SUPPDMSORTSTRING;
        run;
 
        * SORT USING SORTSTRING FROM META DATA;
 
        * create v5 export files;
        libname xpt xport "&gbl_root/xpt/cln_sdtmdm.xpt";
        data xpt.dm;
          set cln.dm;
        run;quit;
        libname xpt clear;
 
        libname xpt xport "&gbl_root/xpt/cln_sdtmsuppdm.xpt";
        data xpt.suppdm;
          set cln.suppdm;
        run;quit;
        libname xpt clear;
 
        %mend cln_500sdtmDm;
    ;;;;
    run;quit;
 
    /*
    PROC CDISC MODEL = SDTM;
     SDTM SDTMVersion = "3.1";
     DOMAINDATA data = cln.Dm
     domain = DM
     category = SPECIAL;
    run;quit;
    */
 
    /*
    %inc "&gbl_root/oto/cln_500sdtmDm.sas";
 
    %cln_500sdtmDm;
    */
 
    /*              _                   _          ____  __  __
      ___ _ __   __| |  _ __ ___   __ _| | _____  |  _ \|  \/  |
     / _ \ `_ \ / _` | | `_ ` _ \ / _` | |/ / _ \ | | | | |\/| |
    |  __/ | | | (_| | | | | | | | (_| |   <  __/ | |_| | |  | |
     \___|_| |_|\__,_| |_| |_| |_|\__,_|_|\_\___| |____/|_|  |_|
 
     ____ ____  ____          _ _                _
    | ___|___ \| ___| ___  __| | |_ _ __ ___    / \   ___
    |___ \ __) |___ \/ __|/ _` | __| `_ ` _ \  / _ \ / _ \
     ___) / __/ ___) \__ \ (_| | |_| | | | | |/ ___ \  __/
    |____/_____|____/|___/\__,_|\__|_| |_| |_/_/   \_\___|
 
    */
 
 
    filename ft15f001 "&gbl_root/oto/cln_525sdtmAe.sas";
    parmcards4;
    %macro cln_525sdtmAe;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=AE);
 
        %put &AEKEEPSTRING;
 
        **** DERIVE THE MAJORITY OF SDTM AE VARIABLES;
        options missing = ' ';
        data ae;
          set EMPTY_AE
          cln.cln_rawadverse;
            studyid = 'XYZ123';
            domain = 'AE';
            usubjid = left(uniqueid);
        run;
 
        proc sort
          data=ae;
            by usubjid;
        run;
 
        data ae;
          merge ae(in=inae) cln.cln_dm(keep=usubjid rfstdtc);
            by usubjid;
 
            if inae;
 
            %cln_110mkesdtmdy(refdate=rfstdtc,date=aestdtc);
            %cln_110mkesdtmdy(refdate=rfstdtc,date=aeendtc);
 
        run;
 
 
        **** CREATE SEQ VARIABLE;
        proc sort
          data=ae;
            by studyid usubjid aedecod aestdtc aeendtc;
        run;
 
        data ae;
          retain STUDYID DOMAIN USUBJID AESEQ AETERM AEDECOD AEBODSYS AESEV AESER AEACN AEREL AESTDTC
                 AEENDTC AESTDY AEENDY;
          set ae(drop=aeseq);
            by studyid usubjid aedecod aestdtc aeendtc;
 
            if not (first.aeendtc and last.aeendtc) then
              put "WARN" "ING: key variables do not define an unique record. " usubjid=;
 
            retain aeseq;
            if first.usubjid then
              aeseq = 1;
            else
              aeseq = aeseq + 1;
 
            label aeseq = "Sequence Number";
        run;
 
 
        **** SORT AE ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=AE);
 
        proc sort
          data=ae(keep = &AEKEEPSTRING)
          out=cln.ae;
            by &AESORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_sdtmAe.xpt";
        data xpt.AE;
          set cln.ae;
        run;quit;
        libname xpt clear;
 
        /*
        Middle Observation(20 ) of cln.ae - Total Obs 40
 
 
         -- CHARACTER --
        STUDYID                 C15      XYZ123              Study Identifier
        DOMAIN                  C2       AE                  Domain Abbreviation
        USUBJID                 C25      UNI305              Unique Subject Identifier
        AETERM                  C200     POUNDING HEART      Reported Term for the Adverse Event
        AEDECOD                 C200     PALPITATIONS        Dictionary-Derived Term
        AEBODSYS                C200     CARDIAC DISORDER    Body System or Organ Class
        AESEV                   C40      severe              Severity/Intensity
        AESER                   C40      N                   Serious Event
        AEACN                   C40      no dose change      Action Taken with Study Treatment
        AEREL                   C40      not                 Causality
        AESTDTC                 C16      2010-10-05          Start Date/Time of Adverse Event
        AEENDTC                 C16      2010-10-05          End Date/Time of Adverse Event
        TOTOBS                  C16      40                  TOTOBS
 
 
         -- NUMERIC --
        AESEQ                   N8       1                   Sequence Number
        AESTDY                  N8                           Study Day of Start of Adverse Event
        AEENDY                  N8                           Study Day of End of Adverse Event
        */
 
    %mend cln_525sdtmAe;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_525sdtmAe.sas";
 
    %cln_525sdtmAe;
    */
 
    /*___ ____   ___          _ _             _____
    | ___| ___| / _ \ ___  __| | |_ _ __ ___ | ____|_  __
    |___ \___ \| | | / __|/ _` | __| `_ ` _ \|  _| \ \/ /
     ___) |__) | |_| \__ \ (_| | |_| | | | | | |___ >  <
    |____/____/ \___/|___/\__,_|\__|_| |_| |_|_____/_/\_\
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_550sdtmEx.sas";
    parmcards4;
    %macro cln_550sdtmEx ;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=EX);
 
        **** DERIVE THE MAJORITY OF SDTM EX VARIABLES;
        data ex;
          set EMPTY_EX
              cln.cln_rawdosing;
 
            studyid = 'XYZ123';
            domain = 'EX';
            usubjid = left(uniqueid);
            exdose = dailydose;
            exdostot = dailydose;
            exdosu = 'mg';
            exdosfrm = 'TABLET, COATED';
            exstdtc=put(startdt,yymmdd10.);
            exendtc=put(enddt,yymmdd10.);
        run;
 
        proc sort
          data=ex;
            by usubjid;
        run;
 
        **** CREATE SDTM STUDYDAY VARIABLES AND INSERT EXTRT;
        data ex;
          merge ex(in=inex) cln.dm(keep=usubjid rfstdtc arm);
            by usubjid;
 
            if inex;
 
            %cln_110mkesdtmdy(refdate=rfstdtc,date=exstdtc);
            %cln_110mkesdtmdy(refdate=rfstdtc,date=exendtc);
 
            **** in this simplistic case all subjects received the treatment they were randomized to;
            extrt = arm;
        run;
 
 
        **** CREATE SEQ VARIABLE;
        proc sort
          data=ex;
            by studyid usubjid extrt exstdtc;
        run;
 
        OPTIONS MISSING = ' ';
        data ex;
          retain STUDYID DOMAIN USUBJID EXSEQ EXTRT EXDOSE EXDOSU EXDOSFRM EXDOSTOT
                 EXSTDTC EXENDTC EXSTDY EXENDY;
          set ex(drop=exseq);
            by studyid usubjid extrt exstdtc;
 
            if not (first.exstdtc and last.exstdtc) then
              put "WARN" "ING: key variables do not define an unique record. " usubjid=;
 
            retain exseq;
            if first.usubjid then
              exseq = 1;
            else
              exseq = exseq + 1;
 
            label exseq = "Sequence Number";
        run;
 
 
        **** SORT EX ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=EX);
 
 
        proc sort
          data=ex(keep = &EXKEEPSTRING)
          out=cln.ex;
            by &EXSORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_sdtmsEx.xpt";
        data xpt.Ex;
          set cln.Ex;
        run;quit;
        libname xpt clear;
 
        /*
        Middle Observation(42 ) of cln.ex - Total Obs 84
 
 
         -- CHARACTER --
        STUDYID                C15      XYZ123              Study Identifier
        DOMAIN                 C2       EX                  Domain Abbreviation
        USUBJID                C25      UNI505              Unique Subject Identifier
        EXTRT                  C40                          Name of Actual Treatment
        EXDOSU                 C40      mg                  Dose Units
        EXDOSFRM               C40      TABLET, COATED      Dose Form
        EXSTDTC                C16      2010-03-13          Start Date/Time of Treatment
        EXENDTC                C16      2010-06-15          End Date/Time of Treatment
 
         -- NUMERIC --
        EXSEQ                  N8       1                   Sequence Number
        EXDOSE                 N8       2                   Dose per Administration
        EXDOSTOT               N8       2                   Total Daily Dose
        EXSTDY                 N8                           Study Day of Start of Treatment
        EXENDY                 N8                           Study Day of End of Treatment
        */
 
    %mend cln_550sdtmEx;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_550sdtmEx.sas";
 
    %cln_550sdtmEx;
    */
 
 
    /*___ _____ ____          _ _             _     _
    | ___|___  | ___| ___  __| | |_ _ __ ___ | |   | |__
    |___ \  / /|___ \/ __|/ _` | __| `_ ` _ \| |   | `_ \
     ___) |/ /  ___) \__ \ (_| | |_| | | | | | |___| |_) |
    |____//_/  |____/|___/\__,_|\__|_| |_| |_|_____|_.__/
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_575sdtmLb.sas";
    parmcards4;
    %macro cln_575sdtmLb ;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=LB);
 
        proc format;
          value visit_labs_month
            0=baseline
            1=3 months
            2=6 months;
          run;
 
        data lb;
          set EMPTY_LB
              cln.cln_rawlabs;
 
            studyid = 'XYZ123';
            domain = 'LB';
            usubjid = left(uniqueid);
            lborres = left(put(nresult,best.));
            lborresu = left(colunits);
            lbornrlo = left(put(lownorm,best.));
            lbornrhi = left(put(highnorm,best.));
            lbcat = labcat;
            lbtest = labtest;
            lbtestcd = labtest;
 
 
            **** create standardized results;
            lbstresc = lborres;
            lbstresn = nresult;
            lbstresu = lborresu;
            lbstnrlo = lownorm;
            lbstnrhi = highnorm;
 
            if lbstnrlo ne . and lbstresn ne . and
               round(lbstresn,.0000001) < round(lbstnrlo,.0000001) then
              lbnrind = 'LOW';
            else if lbstnrhi ne . and lbstresn ne . and
               round(lbstresn,.0000001) > round(lbstnrhi,.0000001) then
              lbnrind = 'HIGH';
            else if lbstnrhi ne . and lbstresn ne . then
              lbnrind = 'NORMAL';
 
            visitnum = month;
            visit = put(month,visit_labs_month.);
            if visit = 'baseline' then
              lbblfl = 'Y';
                else
                  lbblfl = ' ';
 
            lbdtc = put(labdate,yymmdd10.);
        run;
 
 
        proc sort
          data=lb;
            by usubjid;
        run;
 
        **** CREATE SDTM STUDYDAY VARIABLES;
        data lb;
          merge lb(in=inlb) cln.dm(keep=usubjid rfstdtc);
            by usubjid;
 
            if inlb;
 
            %cln_110mkesdtmdy(refdate=rfstdtc,date=lbdtc)
        run;
 
 
        **** CREATE SEQ VARIABLE;
        proc sort
          data=lb;
            by studyid usubjid lbtestcd visitnum;
        run;
 
        data lb;
          retain STUDYID DOMAIN USUBJID LBSEQ LBTESTCD LBTEST LBCAT LBORRES LBORRESU LBORNRLO LBORNRHI
                 LBSTRESC LBSTRESN LBSTRESU LBSTNRLO LBSTNRHI LBNRIND LBBLFL VISITNUM VISIT LBDTC LBDY;
          set lb(drop=lbseq);
            by studyid usubjid lbtestcd visitnum;
 
            if not (first.visitnum and last.visitnum) then
              put "WARN" "ING: key variables do not define an unique record. " usubjid=;
 
            retain lbseq;
            if first.usubjid then
              lbseq = 1;
            else
              lbseq = lbseq + 1;
 
            label lbseq = "Sequence Number";
        run;
 
 
        **** SORT LB ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=LB);
 
        proc sort
          data=lb(keep = &LBKEEPSTRING)
          out=cln.lb;
            by &LBSORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_sdtmLb.xpt";
        data xpt.Lb;
          set cln.Lb;
        run;quit;
        libname xpt clear;
 
    %mend cln_575sdtmLb;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_575sdtmLb.sas";
 
    %cln_575sdtmLb;
    */
 
 
    /*__    ___   ___          _ _            __  __
     / /_  / _ \ / _ \ ___  __| | |_ _ __ ___ \ \/ /_ __
    | `_ \| | | | | | / __|/ _` | __| `_ ` _ \ \  /| `_ \
    | (_) | |_| | |_| \__ \ (_| | |_| | | | | |/  \| |_) |
     \___/ \___/ \___/|___/\__,_|\__|_| |_| |_/_/\_\ .__/
                                                   |_|
    */
 
    filename ft15f001 "&gbl_root/oto/cln_600sdtmXp.sas";
    parmcards4;
    %macro cln_600sdtmXp ;
 
        *---------------------------------------------------------------*;
        * XP.sas creates the SDTM XP dataset and saves it
        * as a permanent SAS datasets to the sdtm libref.
        *---------------------------------------------------------------*;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=XP);
 
        proc format;
          value pain
            0='None'
            1='Mild'
            2='Moderate'
            3='Severe';
          value visit_labs_month
            0='Baseline visit'
            1='Month 3 visit'
            2='Month 6 visit';
        run;
 
        **** DERIVE THE MAJORITY OF SDTM XP VARIABLES;
 
        data xp;
          set empty_xp cln.cln_rawpain;
            studyid = 'XYZ123';
            domain = 'XP';
            usubjid = left(uniqueid);
 
            xptest = 'Pain Score';
            xptestcd = 'XPPAIN';
 
            **** transpose pain data;
            array dates {3} randomizedt month3dt month6dt;
            array scores {3} painbase pain3mo pain6mo;
 
            do i = 1 to 3;
              visitnum = i - 1;
              visit = put(visitnum,visit_labs_month.);
              if scores{i} ne . then
                do;
                  xporres = left(put(scores{i},pain.));
                  xpstresc = xporres;
                  xpstresn = scores{i};
                  xpdtc = put(dates{i},yymmdd10.);
                  output;
                end;
            end;
            *drop randomizedt month3dt month6dt painbase pain3mo pain6mo i;
        run;
 
 
        proc sort
          data=xp;
            by usubjid;
        run;
 
        **** CREATE SDTM STUDYDAY VARIABLES;
        data xp;
          merge xp(in=inxp) cln.dm(keep=usubjid rfstdtc);
            by usubjid;
 
            if inxp;
 
            %cln_110mkesdtmdy(refdate=rfstdtc,date=xpdtc);
 
        run;
 
 
        **** CREATE SEQ VARIABLE;
        proc sort
          data=xp;
            by studyid usubjid xptestcd visitnum;
        run;
 
 
        data xp;
          retain STUDYID DOMAIN USUBJID XPSEQ XPTESTCD XPTEST XPORRES VISITNUM VISIT XPDTC XPDY;
          set xp(drop=xpseq);
            by studyid usubjid xptestcd visitnum;
 
            if not (first.visitnum and last.visitnum) then
              put "WARN" "ING: key variables do not define an unique record. " usubjid=;
 
            retain xpseq;
            if first.usubjid then
              xpseq = 1;
            else
              xpseq = xpseq + 1;
 
            label xpseq = "Sequence Number";
        run;
 
        **** SORT XP ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=XP);
 
        proc sort
          data=xp(keep = &XPKEEPSTRING)
          out=cln.xp;
            by &XPSORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_sdtmsXp.xpt";
        data xpt.Xp;
          set cln.Xp;
        run;quit;
        libname xpt clear;
 
        /*
         Middle Observation(88 ) of cln.xp - Total Obs 177
 
 
          -- CHARACTER --
         STUDYID              C15      XYZ123              Study Identifier
         DOMAIN               C2       XP                  Domain Abbreviation
         USUBJID              C25      UNI507              Unique Subject Identifier
         XPTESTCD             C8       XPPAIN              Pain Test Short Name
         XPTEST               C40      Pain Score          Pain Test Name
         XPORRES              C200     Severe              Result or Finding in Original Units
         VISIT                C200     baseline            Visit Name
         XPDTC                C16      2010-04-06          Date/Time of  Collection
         XPSTRESC             C200     Severe              Result or Finding in Standard Format
         TOTOBS               C16      177                 TOTOBS
 
 
          -- NUMERIC --
         XPSEQ                N8       1                   Sequence Number
         VISITNUM             N8       0                   Visit Number
         XPDY                 N8       1                   Study Day of  Collection
         XPSTRESN             N8       3                   Numeric Result/Finding in Standard Units
        */
 
    %mend cln_600sdtmXp;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_600sdtmXp.sas";
 
    %cln_600sdtmXp;
    */
 
    /*
    FINAL STATE OF DIRECTORIES
 
    - added files
 
    Directory of &gbl_root/cln
 
        cln_fmt.sas7bcat            * formats
 
        cln_rawadverse.sas7bdat     * raw data
        cln_rawdemographic.sas7bdat
        cln_rawdosing.sas7bdat
        cln_rawlabs.sas7bdat
        cln_rawpain.sas7bdat
 
        ae.sas7bdat      *sdtms;
        dm.sas7bdat
        ex.sas7bdat
        lb.sas7bdat
        suppdm.sas7bdat
        xp.sas7bdat
 
       <DIR>  b64
                  cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                  cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
 
                  cln_rawadverse.b64     * base 64 encoded raw sas datasets;
                  cln_rawdemographic.b64
                  cln_rawdosing.b64
                  cln_rawlabs.b64
                  cln_rawpain.b64
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
                  cln_010.sas            *  SAS Tools
 
                  cln_100makedefine.sas  * CDISC utilities ( extract meta data from excel tabs;
                  cln_110mkesdtmdy.sas
                  cln_120mkesrtodr.sas
                  cln_130cfb.sas
                  cln_140mkeMtyTbl.sas
                  cln_150domainx.sas
                  cln_160dtc2dt.sas
                  cln_170mrgsup.sas
 
                - cln_500sdtmDm.sas     * create sdtm datasets DM;
                - cln_525sdtmAe.sas     * AE
                - cln_550sdtmEx.sas     * EX
                - cln_575sdtmLb.sas     * LB
                - cln_600sdtmXp.sas     * XP
 
 
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
 
       <DIR>  xls
                  cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                  cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
 
                  cln_adamDefine.xml           * concatenation of all odm sections below
 
                  cln_adamdefine_header.xml    * excel odm section created from excel tab header
                  cln_adamanalysisresults.xml  ..
                  cln_adamcompmethod.xml
                  cln_adamitemdef.xml
                  cln_adamitemdef_value.xml
                  cln_adamitemgroupdef.xml
                  cln_adamleaves.xml
                  cln_adamvaluelist.xml
                  cln_sdtmcompmethod.xml
 
                  cln_sdtmDefine.xml         * concatenation of all stdm sections below
 
                  cln_sdtmdefine_header.xml  * excel odm section created from excel tab header
                  cln_sdtmitemdef.xml        ..
                  cln_sdtmitemdef_value.xml
                  cln_sdtmitemgroupdef.xml
                  cln_sdtmvaluelist.xml
       <DIR>  xpt
                - cln_sdtmDm.xpt
                - cln_sdtmAe.xpt
                - cln_sdtmsEx.xpt
                - cln_sdtmsuppdm.xpt
                - cln_sdtmsXp.xpt
    */
 
    /*  _    ____        __  __
       / \  |  _ \  __ _|  \/  |
      / _ \ | | | |/ _` | |\/| |
     / ___ \| |_| | (_| | |  | |
    /_/   \_\____/ \__,_|_|  |_|
      __   _ ____            _                    _       _     _
     / /_ / | ___|  __ _  __| | __ _ _ __ ___    / \   __| |___| |
    | `_ \| |___ \ / _` |/ _` |/ _` | `_ ` _ \  / _ \ / _` / __| |
    | (_) | |___) | (_| | (_| | (_| | | | | | |/ ___ \ (_| \__ \ |
     \___/|_|____/ \__,_|\__,_|\__,_|_| |_| |_/_/   \_\__,_|___/_|
 
    */
 
 
    filename ft15f001 "&GBL_ROOT/OTO/CLN_615ADAMADSl.SAS";
 
    parmcards4;
    %macro cln_615adamAdsl ;
 
        *------------------------------------------------------------*;
        * ADSL.sas creates the ADaM ADSL data set
        * as permanent SAS datasets to the ADaM libref.
        *------------------------------------------------------------*;
 
 
        **** CREATE EMPTY ADSL DATASET CALLED EMPTY_ADSL;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx,dataset=ADSL);
 
        %cln_170mrgsup(sourcelib=cln, domains=DM);
 
        ** find the change from baseline so that responders can be flagged ;
        ** (2-point improvement in pain at 6 months);
 
 
        %cln_130cfb(indata=cln.xp, outdata=responders, dayvar=xpdy, avalvar= xpstresn,
             keepvars=usubjid visitnum chg);
 
        data ADSL;
            merge EMPTY_ADSL
                        cln.DM         (in = inDM)
                        responders (in = inresp where=(visitnum=2))
                        ;
              by usubjid;
 
                * convert RFSTDTC to a numeric SAS date named TRTSDT;
                %cln_160dtc2dt (RFSTDTC, prefix=TRTS );
 
                * create BRTHDT, RANDDT, TRTEDT;
                %cln_160dtc2dt (BRTHDTC, prefix=BRTH);
                %cln_160dtc2dt (RANDDTC, prefix=RAND);
                %cln_160dtc2dt (RFENDTC, prefix=TRTE);
 
                * created flags for ITT and safety-evaluable;
                ittfl = put(randdt, popfl.);
                saffl = put(trtsdt, popfl.);
 
                trt01p = ARM;
                trt01a = trt01p;
                trt01pn = input(put(trt01p, $trt01pn.), best.);
                trt01an = trt01pn;
                agegr1n = input(put(age, agegr1n.), best.);
                agegr1  = put(agegr1n, agegr1_.);
                RESPFL = put((.z <= chg <= -2), _0n1y.);
        run;
 
        **** SORT ADSL ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
 
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx, dataset=ADSL);
 
        proc sort
          data=adsl
          (keep = &ADSLKEEPSTRING)
          out=cln.adsl;
            by &ADSLSORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_adamadsl.xpt";
        data xpt.adsl;
          set cln.adsl;
        run;quit;
        libname xpt clear;
 
        /*
        Middle Observation(30 ) of cln.adsl - Total Obs 60
 
 
         -- CHARACTER --
        STUDYID             C15      XYZ123        Study Identifier
        USUBJID             C25      UNI506        Unique Subject Identifier
        SUBJID              C7       506           Subject Identifier for the Study
        SITEID              C7       500           Study Site Identifier
        AGEU                C5       YEARS         Age Units
        AGEGR1              C40      <55 YEARS     Pooled Age Group 1
        SEX                 C1       W             Sex
        RACE                C40      White         Race
        RACEOTH             C40                    Race, Other, Specify
        ARM                 C40      Placebo       Description of Planned Arm
        TRT01P              C40      Placebo       Planned Treatment for Period 01
        TRT01A              C40      Placebo       Actual Treatment for Period 01
        COUNTRY             C3       USA           Country
        ITTFL               C1       N             Intent-To-Treat Population Flag
        SAFFL               C1       Y             Safety Population Flag
        RESPFL              C1       N             Efficacy Responder Flag
        TOTOBS              C16      60            TOTOBS
 
 
         -- NUMERIC --
        RANDDT              N8                     Date of Randomization
        TRTSDT              N8       18282         Date of First Exposure to Treatment
        TRTEDT              N8       18463         Date of Last Exposure to Treatment
        BRTHDT              N8       1664          Date of Birth
        AGE                 N8       45            Age
        AGEGR1N             N8       1             Pooled Age Group 1 (N)
        TRT01PN             N8       0             Planned Treatment for Period 01 (N)
        TRT01AN             N8       0             Actual Treatment for Period 01 (N)
        */
 
    %mend cln_615adamAdsl;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_615adamAdsl.sas";
 
    %cln_615adamAdsl;
    */
 
 
 
 
    /*
      _   ____  ____            _                    _    _____
     / /_|___ \| ___|  __ _  __| | __ _ _ __ ___    / \  | ____|
    | `_ \ __) |___ \ / _` |/ _` |/ _` | `_ ` _ \  / _ \ |  _|
    | (_) / __/ ___) | (_| | (_| | (_| | | | | | |/ ___ \| |___
     \___/_____|____/ \__,_|\__,_|\__,_|_| |_| |_/_/   \_\_____|
 
    */
 
 
 
    filename ft15f001 "&gbl_root/oto/cln_625adamAdae.sas";
    parmcards4;
    %macro cln_625adamAdae ;
    *---------------------------------------------------------------*;
    * ADAE.sas creates the ADaM ADAE-structured data set
    * for AE data (ADAE), saved to the ADaM libref.
    *---------------------------------------------------------------*;
 
    **** CREATE EMPTY ADAE DATASET CALLED EMPTY_ADAE;
 
    %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx,dataset=ADAE);
 
    %put &=adaekeepstring;
 
    proc sort
      data = cln.adsl
      (keep = usubjid siteid country age agegr1 agegr1n sex race trtsdt trt01a trt01an saffl)
      out = adsl;
        by usubjid;
    run;quit;
 
    data adae;
      /*merge sdtm.ae (in = inae) adsl (in = inadsl); */
      merge cln.ae (in = inae) cln.adsl (in = inadsl);
        by usubjid ;
 
            if inae and not inadsl then
              put 'PROB' 'LEM: Subject missing from ADSL?-- ' usubjid= inae= inadsl= ;
 
            rename trt01a    = trta
                   trt01an   = trtan
            ;
            if inadsl and inae;
 
            %cln_160dtc2dt(aestdtc, prefix=ast, refdt=trtsdt);
            %cln_160dtc2dt(aeendtc, prefix=aen, refdt=trtsdt);
 
            if index(AEDECOD, 'PAIN')>0 or AEDECOD='HEADACHE' then
              CQ01NAM = 'PAIN EVENT';
            else
              CQ01NAM = '          ';
 
            aereln = input(put(upcase(aerel), $aereln.), best.);
            aesevn = input(put(upcase(aesev), $aesevn.), best.);
            relgr1n = (aereln); ** group related events (AERELN>0);
            relgr1  = put(relgr1n, relgr1n.);
            if astdt>=trtsdt then
              trtemfl = 'Y';
            format astdt aendt yymmdd10.;
    run;quit;
 
    ** ASSIGN VARIABLE ORDER AND LABELS;
 
    data adae;
      retain &adaeKEEPSTRING;
      set EMPTY_adae adae;
    run;
 
    **** SORT adae ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
 
    %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx, dataset=ADAE);
 
    proc sort
      data=adae(keep = &adaeKEEPSTRING)
      out=cln.adae;
        by &adaeSORTSTRING;
    run;
 
    libname xpt xport "&gbl_root/xpt/cln_adamadae.xpt";
    data xpt.adae;
      set cln.adae;
    run;quit;
    libname xpt clear;
 
 
    /*
    Middle Observation(20 ) of cln.adae - Total Obs 40
 
 
     -- CHARACTER --
    STUDYID              C15      XYZ123              Study Identifier
    USUBJID              C25      UNI305              Unique Subject Identifier
    SITEID               C7       300                 Study Site Identifier
    COUNTRY              C3       USA                 Country
    AGEGR1               C20      <55 YEARS           Pooled Age Group 1
    SEX                  C1       W                   Sex
    TRTA                 C40      Active              Actual Treatment
    AETERM               C200     POUNDING HEART      Reported Term for the Adverse Event
    AEDECOD              C200     PALPITATIONS        Dictionary-Derived Term
    AEBODSYS             C200     CARDIAC DISORDER    Body System or Organ Class
    SAFFL                C1       Y                   Safety Population Flag
    AESEV                C40      severe              Severity/Intensity
    AESER                C40      N                   Serious Event
    AEACN                C40      no dose change      Action Taken with Study Treatment
    AEREL                C40      not                 Causality
    CQ01NAM              C200                         CQ 01 Name
    RELGR1               C10      NOT RELATE          Pooled Causality Group 1
    TRTEMFL              C1       Y                   Treatment Emergent Flag
    TOTOBS               C16      40                  TOTOBS
 
 
     -- NUMERIC --
    AESEQ                N8       1                   Sequence Number
    AGE                  N8       44                  Unique Subject Identifier
    AGEGR1N              N8       1                   Pooled Age Group 1 (N)
    TRTAN                N8                           Actual Treatment (N)
    ASTDT                N8       18540               Start Date/Time of Adverse Events
    AENDT                N8       18540               End Date/Time of Adverse Events
    ASTDY                N8       118                 Study Day of Start of Adverse Event
    AESEVN               N8       3                   Severity/Intensity (N)
    AENDY                N8       118                 Study Day of End of Adverse Event
    AERELN               N8       0                   Causality (N)
    RELGR1N              N8       0                   Pooled Causality Group 1 (N)
    */
 
    %mend cln_625adamAdAe;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_625adamAdae.sas";
 
    %cln_625adamAdAe;
    */
 
 
 
    /*__  ____   ___            _                    _       _       __
     / /_| ___| / _ \  __ _  __| | __ _ _ __ ___    / \   __| | ___ / _|
    | `_ \___ \| | | |/ _` |/ _` |/ _` | `_ ` _ \  / _ \ / _` |/ _ \ |_
    | (_) |__) | |_| | (_| | (_| | (_| | | | | | |/ ___ \ (_| |  __/  _|
     \___/____/ \___/ \__,_|\__,_|\__,_|_| |_| |_/_/   \_\__,_|\___|_|
 
    */
 
    filename ft15f001 "&gbl_root/oto//cln_650adamAdef.sas";
    parmcards4;
    %macro cln_650adamAdef ;
        *---------------------------------------------------------------*;
        * ADEF.sas creates the ADaM BDS-structured data set
        * for efficacy data (ADEF), saved to the ADaM libref.
        *---------------------------------------------------------------*;
 
        **** CREATE EMPTY ADEF DATASET CALLED EMPTY_ADEF;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx,dataset=ADEF);
 
        %cln_130cfb(indata=cln.xp, outdata=adef, dayvar=xpdy, avalvar= xpstresn);
 
        proc sort
          data = cln.adsl
          (keep = usubjid siteid country age agegr1 agegr1n sex race randdt trt01p trt01pn ittfl)
          out = adsl;
            by usubjid;
        run;quit;
 
        data adef;
          merge adef (in = inadef) adsl (in = inadsl);
            by usubjid ;
 
                if not(inadsl and inadef) then
                  put 'PROB' 'LEM: Missing subject?-- ' usubjid= inadef= inadsl= ;
 
                rename trt01p    = trtp
                       trt01pn   = trtpn
                       xptest    = param
                       xptestcd  = paramcd
                       visit     = avisit
                       xporres   = avalc
                ;
                if inadsl and inadef;
                avisitn = input(put(visitnum, avisitn.), best.);
 
                %cln_160dtc2dt(xpdtc, refdt=randdt);
 
                retain crit1 "Pain improvement from baseline of at least 2 points";
                RESPFL = put((.z <= chg <= -2), _0n1y.);
                if RESPFL='Y' then
                  crit1fl = 'Y';
                else
                  crit1fl = 'N';
        run;
 
        ** assign variable order and labels;
        data adef;
          retain &ADEFKEEPSTRING;
          set EMPTY_ADEF adef;
        run;
 
        **** SORT ADEF ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
 
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx, dataset=ADEF);
 
        %put &=ADEFKEEPSTRING;
        %put &=ADEFSORTSTRING;
 
        /*
        proc sort
          data=adef(keep = &ADEFKEEPSTRING)
          out=cln.adef;
            by &ADEFSORTSTRING; * not in meta data?;
        run;
        */
 
        data cln.adef;
           set adef(keep = &ADEFKEEPSTRING);
        run;quit;
 
        libname xpt xport "&gbl_root/xpt/cln_adamadef.xpt";
        data xpt.adef;
          set cln.adef;
        run;quit;
        libname xpt clear;
 
        /*
        Middle Observation(88 ) of cln.adef - Total Obs 177
 
 
         -- CHARACTER --
        STUDYID          C15      XYZ123              Study Identifier
        USUBJID          C25      UNI507              Unique Subject Identifier
        AGEGR1           C20      <55 YEARS           Pooled Age Group 1
        SEX              C1       W                   Sex
        SITEID           C7       500                 Study Site Identifier
        TRTP             C40      Active              Planned Treatment
        PARAMCD          C8       XPPAIN              Parameter Code
        PARAM            C40      Pain Score          Parameter
        COUNTRY          C3       USA                 Country
        AVISIT           C16      baseline            Analysis Visit
        ABLFL            C1       Y                   Baseline Record Flag
        AVALC            C12      Severe              Analysis Value (C)
        CRIT1FL          C1       N                   Criterion 1 Evaluation Result Flag
        CRIT1            C60      Pain improvement    Analysis Criterion 1
        ITTFL            C1       N                   Intent-to-Treat Flag
        TOTOBS           C16      177                 TOTOBS
 
 
         -- NUMERIC --
        AGE              N8       33                  Age
        AGEGR1N          N8       1                   Pooled Age Group 1 (N)
        RANDDT           N8                           Date of Randomization
        TRTPN            N8                           Planned Treatment (N)
        AVISITN          N8       0                   Analysis Visit (N)
        XPSEQ            N8       1                   Sequence Number
        VISITNUM         N8       0                   Visit Number
        ADT              N8       18358               Analysis Date
        ADY              N8                           Analysis Relative Day
        AVAL             N8       3                   Analysis Value
        BASE             N8       3                   Baseline Value
        CHG              N8       0                   Change from Baseline
       */
 
 
    %mend cln_650adamAdef;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto//cln_650adamAdef.sas";
 
    %cln_650adamAdef;
    */
 
    /*__ _____ ____            _                    _       _ _____ _
     / /|___  | ___|  __ _  __| | __ _ _ __ ___    / \   __| |_   _| |_ ___
    | `_ \ / /|___ \ / _` |/ _` |/ _` | `_ ` _ \  / _ \ / _` | | | | __/ _ \
    | (_) / /  ___) | (_| | (_| | (_| | | | | | |/ ___ \ (_| | | | | ||  __/
     \___/_/  |____/ \__,_|\__,_|\__,_|_| |_| |_/_/   \_\__,_| |_|  \__\___|
 
    */
 
    filename ft15f001 "&gbl_root/oto/cln_675adamAdTte.sas";
    parmcards4;
    %macro cln_675adamAdTte ;
        *---------------------------------------------------------------*;
        * ADTTE.sas creates the ADaM BDS-structured data set
        * for a time-to-event analysis (ADTTE), saved to the ADaM libref.
        *---------------------------------------------------------------*;
 
        **** CREATE EMPTY ADTTE DATASET CALLED EMPTY_ADTTE;
 
        %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx,dataset=ADTTE);
 
        %put &=adttekeepstring;
 
        proc sort
          data = cln.adsl
          (keep = studyid usubjid siteid country age agegr1 agegr1n sex race randdt trt01p trt01pn
                  ittfl trtedt)
          out = adtte;
            by usubjid;
        run;quit;
 
        proc sort
          data = cln.adef
          (keep = usubjid paramcd chg adt visitnum xpseq)
          out = adef;
            where paramcd='XPPAIN' and visitnum>0 and (chg<0 or chg>0);
            by usubjid adt;
        run;quit;
 
        data adef;
          set adef;
            by usubjid adt;
 
                drop paramcd visitnum;
                if first.usubjid;
        run;
 
        proc sort
          data = cln.adae
          (keep = usubjid cq01nam astdt trtemfl aeseq)
          out = adae;
            where cq01nam ne '' and trtemfl='Y';
            by usubjid astdt;
        run;
 
        ** keep only the first occurence of a pain event;
        data adae;
          set adae;
            by usubjid astdt;
 
                if first.usubjid;
        run;
 
        ** get the sequence number for the last EX record;
        proc sort
          data = cln.ex
          (keep = usubjid exseq)
          out = lstex
          nodupkey;
            by usubjid exseq;
        run;quit;
 
        data lstex;
          set lstex;
            by usubjid exseq;
                if last.usubjid;
        run;quit;
 
        data adtte;
          merge adtte (in = inadtte rename=(randdt=startdt))
                adef  (in = inadef)
                adae  (in = inadae)
                lstex (in = inlstex)
                ;
            by usubjid ;
 
                retain param "TIME TO FIRST PAIN RELIEF" paramcd "TTPNRELF";
                rename trt01p    = trtp
                       trt01pn   = trtpn
                ;
 
                length srcvar $10. srcdom $4.;
 
                if (.<chg<0) and (adt<astdt or not inadae) then
                  do;
                    cnsr = 0;
                    adt  = adt;
                    evntdesc = put(cnsr, evntdesc.) ;
                    srcdom = 'ADEF';
                    srcvar = 'XPDY';
                    srcseq = xpseq;
                  end;
                else if chg>0 and (adt<astdt or not inadae) then
                  do;
                    cnsr = 1;
                    adt  = adt;
                    evntdesc = put(cnsr, evntdesc.) ;
                    srcdom = 'XP';
                    srcvar = 'XPDY';
                    srcseq = xpseq;
                  end;
                else if (.<astdt<adt) then
                  do;
                    cnsr = 2;
                    adt  = astdt;
                    evntdesc = put(cnsr, evntdesc.) ;
                    srcdom = 'ADAE';
                    srcvar = 'ASTDY';
                    srcseq = aeseq;
                  end;
                else
                  do;
                    cnsr = 3;
                    adt  = trtedt;
                    evntdesc = put(cnsr, evntdesc.) ;
                    srcdom = 'ADSL';
                    srcvar = 'TRTEDT';
                    srcseq = .;
                  end;
 
                aval = adt - startdt + 1;
 
                format startdt adt yymmdd10.;
        run;
 
        ** assign variable order and labels;
        data adtte;
          retain &adtteKEEPSTRING;
          set EMPTY_adtte adtte;
        run;
 
        **** SORT adtte ACCORDING TO METADATA AND SAVE PERMANENT DATASET;
 
        %cln_120mkesrtodr(metadatafile=&gbl_root/xls/cln_adamMetadata.xlsx, dataset=ADTTE);
        %put &=adttekeepstring;
 
        proc sort
          data=adtte(keep = &adtteKEEPSTRING )
          out=cln.adtte;
            by &adtteSORTSTRING;
        run;
 
        libname xpt xport "&gbl_root/xpt/cln_adamadtte.xpt";
        data xpt.adtte;
          set cln.adtte;
        run;quit;
        libname xpt clear;
 
        /*
        Middle Observation(30 ) of cln.adtte - Total Obs 60
 
 
         -- CHARACTER --
        STUDYID            C15      XYZ123              Study Identifier
        USUBJID            C25      UNI506              Domain Abbreviation
        AGEGR1             C20      <55 YEARS           Pooled Age Group 1
        SEX                C1       W                   Sex
        SITEID             C7       500                 Study Site Identifier
        TRTP               C40      Placebo             Planned Treatment
        PARAMCD            C8       TTPNRELF            Parameter Code
        PARAM              C40      TIME TO FIRST PA    Parameter
        COUNTRY            C3       USA                 Country
        EVNTDESC           C40      COMPLETED STUDY     Event or Censoring Description
        SRCDOM             C6       ADSL                Source Domain
        SRCVAR             C8       TRTEDT              Source Variable
        ITTFL              C1       N                   Intent-to-Treat Flag
        TOTOBS             C16      60                  TOTOBS
 
 
         -- NUMERIC --
        AGE                N8       45                  Unique Subject Identifier
        AGEGR1N            N8       1                   Pooled Age Group 1 (N)
        TRTPN              N8       0                   Planned Treatment (N)
        ADT                N8       18463               Analysis Date
        AVAL               N8                           Analysis Value
        STARTDT            N8                           Time to Event Origin Date for Subject
        CNSR               N8       3                   Censor
        SRCSEQ             N8                           Source Sequence Number
        */
 
    %mend cln_675adamAdTte;
    ;;;;
    run;quit;
 
    /*
    %inc "&gbl_root/oto/cln_675adamAdTte.sas";
 
    %cln_675adamAdTte;
    */
 
    /*
 
    FINAL STATE OF DIRECTORIES
 
    - added files
 
    Directory of &gbl_root/cln
 
        cln_fmt.sas7bcat            * formats
 
        cln_rawadverse.sas7bdat     * raw data
        cln_rawdemographic.sas7bdat
        cln_rawdosing.sas7bdat
        cln_rawlabs.sas7bdat
        cln_rawpain.sas7bdat
 
        adae.sas7bdat   * adams;
        adef.sas7bdat
        adsl.sas7bdat
        adtte.sas7bdat
 
        ae.sas7bdat      *sdtms;
        dm.sas7bdat
        ex.sas7bdat
        lb.sas7bdat
        suppdm.sas7bdat
        xp.sas7bdat
 
       <DIR>  b64
                  cln_adamMetadata.b64   *base 64 of excel mata data for adam datasets;
                  cln_sdtmMetadata.b64   *base 64 of excel mata data for sdtm datasets;
 
                  cln_rawadverse.b64     * base 64 encoded raw sas datasets;
                  cln_rawdemographic.b64
                  cln_rawdosing.b64
                  cln_rawlabs.b64
                  cln_rawpain.b64
       <DIR>  log
       <DIR>  oto
                  cln_010.sas            * many macro tools;
                  cln_010.sas            *  SAS Tools
 
                  cln_100makedefine.sas  * CDISC utilities ( extract meta data from excel tabs;
                  cln_110mkesdtmdy.sas
                  cln_120mkesrtodr.sas
                  cln_130cfb.sas
                  cln_140mkeMtyTbl.sas
                  cln_150domainx.sas
                  cln_160dtc2dt.sas
                  cln_170mrgsup.sas
 
                  cln_500sdtmDm.sas     * create sdtm datasets DM;
                  cln_525sdtmAe.sas     * AE
                  cln_550sdtmEx.sas     * EX
                  cln_575sdtmLb.sas     * LB
                  cln_600sdtmXp.sas     * XP
 
                - cln_615adamAdsl.sas   * create adam datasets ADSL
                - cln_625adamAdae.sas   * ADSL
                - cln_650adamAdef.sas   * ADAE
                - cln_675adamAdTte.sas  * ADEF
 
       <DIR>  pdf
       <DIR>  pin
       <DIR>  png
       <DIR>  rtf
       <DIR>  sas
 
       <DIR>  xls
                  cln_adamMetadata.xlsx  * excel mata data for adam datasets;
                  cln_sdtmMetadata.xlsx  * excel mata data for sdtm datasets;
       <DIR>  xml
 
                  cln_adamDefine.xml           * concatenation of all odm sections below
 
                  cln_adamdefine_header.xml    * excel odm section created from excel tab header
                  cln_adamanalysisresults.xml  ..
                  cln_adamcompmethod.xml
                  cln_adamitemdef.xml
                  cln_adamitemdef_value.xml
                  cln_adamitemgroupdef.xml
                  cln_adamleaves.xml
                  cln_adamvaluelist.xml
                  cln_sdtmcompmethod.xml
 
                  cln_sdtmDefine.xml         * concatenation of all stdm sections below
 
                  cln_sdtmdefine_header.xml  * excel odm section created from excel tab header
                  cln_sdtmitemdef.xml        ..
                  cln_sdtmitemdef_value.xml
                  cln_sdtmitemgroupdef.xml
                  cln_sdtmvaluelist.xml
       <DIR>  xpt
                - cln_adamadtte.xpt
                - cln_adamadae.xpt
                - cln_adamadef.xpt
                - cln_adamadsl.xpt
 
                  cln_sdtmAe.xpt
                  cln_sdtmdm.xpt
                  cln_sdtmsEx.xpt
                  cln_sdtmsuppdm.xpt
                  cln_sdtmsXp.xpt
    */
 
    /*   _      _
      __| |_ __(_)_   _____ _ __
     / _` | `__| \ \ / / _ \ `__|
    | (_| | |  | |\ V /  __/ |
     \__,_|_|  |_| \_/ \___|_|
 
    */
 
    %utlopts;
 
    * just in case you change a macro and forget to overwrite the one in sasmacr;
 
    %inc "&gbl_root/oto/cln_010.sas" / nosource;;
    %inc "&gbl_root/oto/cln_100makedefine.sas" / nosource;
    %inc "&gbl_root/oto/cln_110mkesdtmdy.sas" / nosource;
    %inc "&gbl_root/oto/cln_120mkesrtodr.sas" / nosource;
    %inc "&gbl_root/oto/cln_130cfb.sas" / nosource;
    %inc "&gbl_root/oto/cln_140mkeMtyTbl.sas" / nosource;
    %inc "&gbl_root/oto/cln_150domainx.sas" / nosource;
    %inc "&gbl_root/oto/cln_160dtc2dt.sas" / nosource;
    %inc "&gbl_root/oto/cln_170mrgsup.sas" / nosource;
    %inc "&gbl_root/oto/cln_500sdtmDm.sas" / nosource;
    %inc "&gbl_root/oto/cln_525sdtmAe.sas" / nosource;
    %inc "&gbl_root/oto/cln_550sdtmEx.sas" / nosource;
    %inc "&gbl_root/oto/cln_575sdtmLb.sas" / nosource;
    %inc "&gbl_root/oto/cln_600sdtmXp.sas" / nosource;
    %inc "&gbl_root/oto/cln_615adamAdsl.sas" / nosource;
    %inc "&gbl_root/oto/cln_625adamAdae.sas" / nosource;
    %inc "&gbl_root/oto/cln_650adamAdef.sas" / nosource;
    %inc "&gbl_root/oto/cln_675adamAdTte.sas"/ nosource;
 
 
    %cln_500sdtmDm ;
    %cln_525sdtmAe;
    %cln_550sdtmEx ;
    %cln_575sdtmLb ;
    %cln_600sdtmXp ;
    %cln_615adamAdsl ;
    %cln_625adamAdae ;
    %cln_650adamAdef ;
    %cln_675adamAdTte ;
 
 
    /*____ _   _ ____
    | ____| \ | |  _ \
    |  _| |  \| | | | |
    | |___| |\  | |_| |
    |_____|_| \_|____/
 
    */
