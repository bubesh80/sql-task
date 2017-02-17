# sql-task





import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.stream.Collectors;

import net.sf.jsqlparser.JSQLParserException;
import net.sf.jsqlparser.expression.BinaryExpression;
import net.sf.jsqlparser.expression.Expression;
import net.sf.jsqlparser.expression.Parenthesis;
import net.sf.jsqlparser.expression.operators.conditional.AndExpression;
import net.sf.jsqlparser.expression.operators.conditional.OrExpression;
import net.sf.jsqlparser.expression.operators.relational.ExpressionList;
import net.sf.jsqlparser.expression.operators.relational.InExpression;
import net.sf.jsqlparser.parser.CCJSqlParserUtil;
import net.sf.jsqlparser.schema.Table;
import net.sf.jsqlparser.statement.Statement;
import net.sf.jsqlparser.statement.delete.Delete;
import net.sf.jsqlparser.statement.insert.Insert;
import net.sf.jsqlparser.statement.select.AllColumns;
import net.sf.jsqlparser.statement.select.FromItem;
import net.sf.jsqlparser.statement.select.Join;
import net.sf.jsqlparser.statement.select.PlainSelect;
import net.sf.jsqlparser.statement.select.Select;
import net.sf.jsqlparser.statement.update.Update;
import net.sf.jsqlparser.util.SelectUtils;

import org.apache.commons.collections.CollectionUtils;
import org.junit.Assert;
import org.junit.Test;

import com.worksap.company.hue.hr.coverage.tools.RawDataTool.testCasesData.dto.OrderDetails;
import com.worksap.company.hue.hr.coverage.tools.RawDataTool.testCasesData.logic.TestCaseExecuter;


public class SqlChecker {

    static SqlChecker sqlChecker;
    
    @Test
    public final  void isStringIsImmutable(){
        String t = "george.princelin";
        if(t.contains("."))
        t = t.split("\\.")[1];
        Assert.assertEquals("princelin", t);
    }

    @SuppressWarnings("static-access")
    public static void main(){
    sqlChecker = new SqlChecker();
    
    String unfor = getInsertOfSusi();
    String re = unfor.replaceAll("/", "//");
    
    Statement sel = getStatement(re);
    
    System.exit(0);

    SqlChecker.convertInsertIntoSelect(bugId1901());

    String s = getSpl1();
    System.exit(0);
    differIn();

    Expression select = SqlChecker.getStatementWithExpression(getOr());
    ArrayList list = new ArrayList<>();
    SqlChecker.getAllORExpression(select,list , 0);


    List<String> resultList = new ArrayList<>();

    resultList.add("select * from paykbn where ( pay_kb = 500 or pay_kb = 100 ) and active_kb = 50");




    System.out.println(SqlChecker.decisionScenerio(resultList, (OrderDetails)list.get(0)));

    //        separatedExp(select);
    //        sqlChecker.run();

    //
    //        String result = sqlChecker.checkInExpression(((PlainSelect)body).getWhere(),new StringBuilder(getInExample()));
    //        System.out.println(select);

    }

    private static String getOr(){
    return "select * from paykbn where ( ( pay_kb = 500 and kyuyo_kb = 10 ) or pay_kb = 100 ) and active_kb = 50";
    }


    private static String getNotExistExample(){
    return "SELECT * FROM paykbn WHERE  pay_kb=899 AND "
        + "kyuyo_kb=1 AND NOT EXISTS( SELECT flg05  FROM   "
        + "shirmtterm  WHERE shirmtterm.pay_kb =93 AND  "
        + "shirmtterm.active_kb = 0 AND shirmtterm.flg02 = 0)";
    }

    private static String getExistExample(){
    return "SELECT  ky.sya_id,"
        + "ky.sdate"
        + " ,ky.edate"
        + " ,ky.keitai_cd  "
        + ",ky.keitaij_cd"
        + " ,ky.keitaik_cd "
        + ""+
        "FROM  kyomst ky "
        + "WHERE ky.sdate >= ky.edate AND ky.keitai_cd = 10 AND EXISTS" +
        "(" + "select 1 from "
        + "kgky_wk_taisyo_syain kg where kg.kyuyo_kb = 1 "
        + "and kg.pay_kb   = 100 and kg.sya_id   = ky.sya_id )"
        + "  ORDER  BY ky.sya_id ,ky.sdate";
    }

    private static String getUnionExample(){
    return " SELECT  kg.sya_id,rcm.kyuyo_kb FROM  rui_ck_meisai rcm  ,kgky_wk_taisyo_syain kg WHERE  kg.pay_kb      = ? AND  kg.sya_id  AND  kg.pr_kyuyo_kb = 1"
        +  "AND  rcm.kyuyo_kb   = 1 AND  rcm.stuki BETWEEN kg.fstuki AND kg.tstuki"
        +

             "UNION ALL " +

        "SELECT  kg.sya_id ,rcm.kyuyo_kb FROM  rui_ck_meisai rcm ,kgky_wk_taisyo_syain kg WHERE  kg.pay_kb      = ? " +
        "AND  kg.sya_id      = rcm.sya_id AND  kg.pr_kyuyo_kb = 4  AND  rcm.kyuyo_kb  >= 4  AND  rcm.stuki BETWEEN kg.fstuki AND kg.tstuki "+


           "ORDER BY 1,2,3,4,5"
           ;
    }

    private static String getUpDate(){
    return "UPDATE Customers" +
        " SET ContactName = 'Alfred Schmidt' , City ='Hamburg' " +
        " WHERE CustomerName = 'Alfreds Futterkiste'";
    }

    private static String getCountEx(){
    return "SELECT  count(*) FROM  rui_ck_meisai rcm ,kgky_wk_taisyo_syain kg WHERE ( kg.pay_kb      = 100 OR kg.pay_kb = 500 )" +
        "AND  kg.sya_id      = rcm.sya_id "  ;
    }

    //
    private static String getInExample(){
    return "SELECT  kg.sya_id ,rcm.kyuyo_kb FROM  rui_ck_meisai rcm ,kgky_wk_taisyo_syain kg WHERE  kg.pay_kb      = 100 " +
        "AND  kg.sya_id      = rcm.sya_id AND  kg.pr_kyuyo_kb = 4  "+
        "AND  rcm.kmk_id    in (2125,2135) "  ;
    }

    private static void separatedExp(final Select select){
    select.getSelectBody();
    System.out.println("");
    }

    private static void run(){
    
    }

    /**
     *
     * @param expression
     * @param sql
     * @return
     */
    private String checkInExpression(final Expression expression,StringBuilder sql){
    if(expression instanceof InExpression){

        String leftColumn = ((InExpression)expression).getLeftExpression().toString();


        ExpressionList expressionList = (ExpressionList)((InExpression)expression).getRightItemsList();

        String inValueWithComma = expressionList.getExpressions().stream().map(Expression::toString).map(inValue -> inValue + ",").collect(Collectors.joining());

        String inValueCommaReplace  = inValueWithComma.substring(0,inValueWithComma.length() - 1);
        String leftMaked = leftColumn + " " + "in" + " " + "(" + inValueCommaReplace +  ")";


        String convertedString = expressionList.getExpressions().stream().map(Expression::toString).map(inValue -> {
        return leftColumn + " = " + inValue + " OR ";
        })
        .collect(Collectors.joining());

        String automatedString = "( " + convertedString.substring(0, convertedString.length() - 3) + " )";
        String replaced = sql.toString().replaceAll("( )+", " ").replace(leftMaked, automatedString);
        sql = new StringBuilder(replaced);

    }else if(expression instanceof BinaryExpression){
        checkInExpression(((BinaryExpression)expression).getLeftExpression(),sql);
        checkInExpression(((BinaryExpression)expression).getRightExpression(),sql);
    }
    return sql.toString();
    }


    /**
     *
     * @param selectSql
     * @return
     */
    public static Select getSelectBody(final String selectSql){
    try {
        return (Select)CCJSqlParserUtil.parse(selectSql);
    } catch (JSQLParserException e) {
        throw new RuntimeException("*** Please Pass Select Sql as a proper sql format ***" + e);
    }
    }


    public static Expression getStatementWithExpression(final String selectSql){
    try {
        Statement stmnt = CCJSqlParserUtil.parse(selectSql);
        if(stmnt instanceof Select)
        return ((PlainSelect)((Select)stmnt).getSelectBody()).getWhere();
        else if(stmnt instanceof Update){
        Update updateStatement = (Update)stmnt;
        return updateStatement.getWhere();
        }else if(stmnt instanceof Delete){
        Delete deleteStatement = (Delete)stmnt;
        return deleteStatement.getWhere();
        } else
        throw new RuntimeException("**** Only support select,delete,update");
    } catch (JSQLParserException e) {
        throw new RuntimeException("*** Please Pass Select Sql as a proper sql format ***" + e);
    }
    }

    private static List<String> defaultValue(final int limit){
    ArrayList<String> resultList = new ArrayList<String>();
    for(int i = 1; i <= limit ; i++)
        resultList.add(String.valueOf(i));
    return resultList;
    }

    private void makeMatrixCombination(final Set<String> removedUniqueKeySet){
    new ArrayList<>();
    Map<String, List<String>> matrixMap = new HashMap<>();
    List<String> removedUniqueKeySet1 = Arrays.asList("A","B","C");
    int size = removedUniqueKeySet1.size();

    for(String key : removedUniqueKeySet1)
        matrixMap.put(key, defaultValue(size));

    List<Map<String, String>> uniqueMatrixResultList = new ArrayList<>();

    prepareRecursive(uniqueMatrixResultList, size, removedUniqueKeySet1, matrixMap, new HashMap<>());

    System.out.println(uniqueMatrixResultList);


    }

    private static void prepareRecursive(final List<Map<String, String>> uniqueMatrixResultList, final int size,
        final List<String> commonColumnKeyList,
        final Map<String, List<String>> commonColumnValueMap, final Map<String, String> previousCombinationMap) {
    if (size == 0)
        uniqueMatrixResultList.add(previousCombinationMap);
    else {
        String currentColumnKey = commonColumnKeyList.get(size - 1);
        List<String> currentRandomDataList = commonColumnValueMap.get(currentColumnKey);

        for (String value : currentRandomDataList) {
        Map<String, String> tMap = new HashMap<String, String>();
        tMap.put(currentColumnKey, value);
        tMap.putAll(previousCombinationMap);
        prepareRecursive(uniqueMatrixResultList, size - 1, commonColumnKeyList, commonColumnValueMap, tMap);
        }
    }
    }


    /**
     * Added to fix if there is no primary key in particular table,
     * has to look on uniquenes of the table based on even index also
     * @param tableName
     * @return
     */
    private final static String makeUniqueColumnSql(final String tableName){
    return "select c.COLUMN_NAME from USER_INDEXES i, USER_IND_COLUMNS c "
        + "where "
        + "i.TABLE_NAME = '" + tableName
        + "'and i.UNIQUENESS ='UNIQUE' "
        + "and i.TABLE_NAME = c.TABLE_NAME "
        + "and i.INDEX_NAME = c.INDEX_NAME "
        + "union "
        + "select cc.COLUMN_NAME from USER_CONSTRAINTS con,USER_CONS_COLUMNS cc "
        + "where "
        + "con.TABLE_NAME = '" + tableName + "' "
        + "and con.CONSTRAINT_TYPE in ('U','P') "
        + "and con.TABLE_NAME = cc.TABLE_NAME "
        + "and con.CONSTRAINT_NAME = cc.CONSTRAINT_NAME";
    }

    private static String clientString(final String tableName){
    return "SELECT cols.table_name, cols.column_name, cols.position, cons.status, cons.owner "
        + " FROM all_constraints cons, all_cons_columns cols "
        + " WHERE cols.table_name =" + tableName
        + " AND cons.constraint_type = 'P'"
        + " AND cons.constraint_name = cols.constraint_name "
        + " AND cons.owner = cols.owner "
        + " ORDER BY cols.table_name, cols.position ";
    }

    private static String getOuterJoinExample(){
    return "SELECT * FROM idou id , idou_kojo idk , idou_hdt idh , jlnk_paysyain ps , "
        + "ssikaku sk WHERE  ps.pay_kb = 2 AND  ps.lnk_syori_kb = 3 AND ps.stuki = 201006 "
        + " AND  ps.par_id = 4 AND id.sya_id = idk.sya_id(+) AND id.sya_id = idh.sya_id AND id.seq = idk.seq(+) ";
    }


    ///////////////////////               SAMPLE  DEL   WHOLE FILES         ///////////////////

    //      private void del(){
    //          Path start = new Path("D:\\indata");
    //          Files.walkFileTree(start, new SimpleFileVisitor<Path>() {
    //              @Override
    //              public FileVisitResult visitFile(Path file, FileVisitOption.FOLLOW_LINKS)
    //                  throws IOException
    //              {
    //                  Files.delete(file);
    //                  return FileVisitResult.CONTINUE;
    //              }
    //              @Override
    //              public FileVisitResult postVisitDirectory(Path dir, IOException e)
    //                  throws IOException
    //              {
    //                  if (e == null) {
    //                      Files.delete(dir);
    //                      return FileVisitResult.CONTINUE;
    //                  } else {
    //                      // directory iteration failed
    //                      throw e;
    //                  }
    //              }
    //          });
    //      }


    /**
     *
     * @param expression
     * @param allOrExpression
     * @param order
     */
    private static final void getAllORExpression(final Expression expression,final List<OrderDetails> allOrExpression,final int order){
    if(expression instanceof OrExpression){
        OrderDetails singleObj = new OrderDetails(expression,order);
        allOrExpression.add(singleObj);
        getAllORExpression(((OrExpression)expression).getLeftExpression(),allOrExpression,order+1);
        getAllORExpression(((OrExpression)expression).getRightExpression(),allOrExpression,order+1);
    }else if(expression instanceof AndExpression){
        getAllORExpression(((AndExpression)expression).getLeftExpression(),allOrExpression,order);
        getAllORExpression(((AndExpression)expression).getRightExpression(),allOrExpression,order);
    }else if(expression instanceof Parenthesis)
        getAllORExpression(((Parenthesis)expression).getExpression(),allOrExpression,order);
    }

    private static final List<String> decisionScenerio(final List<String> finalString, final OrderDetails orderDetails){
    List<String> duplicatedList = new ArrayList<>();
    for(String str:finalString){

        String wholeExpression = orderDetails.getExpression().toString().trim();

        if(str.contains(wholeExpression)){
        String leftReplaced = str.replace(wholeExpression, ((OrExpression)orderDetails.getExpression()).getLeftExpression().toString());
        String rightReplaced = str.replace(wholeExpression, ((OrExpression)orderDetails.getExpression()).getRightExpression().toString());
        duplicatedList.add(leftReplaced);
        duplicatedList.add(rightReplaced);
        } else
        duplicatedList.add(str);
    }
    System.out.println(duplicatedList);
    return duplicatedList;
    }

    //////////////////////////////               /////////////////////////////



    private String[] createRuiCkmEisFromFswkSql() {
    StringBuffer rstrbf = new StringBuffer();
    rstrbf.append("insert into rui_ckm_eis( "); //1
    rstrbf.append("kyuyo_kb,");                 //2
    rstrbf.append("pay_kb,");                   //3
    rstrbf.append("sya_id,");                   //4
    rstrbf.append("stuki,");                    //5
    rstrbf.append("fs_cur_cnt,");               //6
    rstrbf.append("kmk_id,");                   //7
    rstrbf.append("skdate,");                   //8
    rstrbf.append("sdate,");                    //9
    rstrbf.append("edate,");                    //10
    rstrbf.append("kingaku,");                  //11
    rstrbf.append("mei1,");                     //12
    rstrbf.append("mei2,");                     //13
    rstrbf.append("tukisu,");                   //14
    rstrbf.append("flg,");                      //15
    rstrbf.append("kr_basedate,");              //16
    rstrbf.append("prc_date)");                 //17
    rstrbf.append("select ");
    rstrbf.append("ck.kyuyo_kb,");
    rstrbf.append("ck.pay_kb,");
    rstrbf.append("ck.sya_id,");
    rstrbf.append("ck.stuki,");
    rstrbf.append("max(ck.fs_cur_cnt),");
    rstrbf.append("ck.kmk_id,");
    rstrbf.append("max(ck.skdate),");
    rstrbf.append("min(ck.sdate),");
    rstrbf.append("max(ck.edate),");
    rstrbf.append("sum(ck.kingaku),");
    rstrbf.append("sum(ck.mei1),");
    rstrbf.append("sum(ck.mei2),");
    rstrbf.append("sum(ck.tukisu),");
    rstrbf.append("max(ck.flg),");
    rstrbf.append("max(ck.kr_basedate),");

    rstrbf.append("from fswk_ckm_eis ck ");
    rstrbf.append("where ck.kyuyo_kb = 100 ");
    rstrbf.append("  and ck.pay_kb = 10 ");
    //>>>>>>>>>>>>>>>2012/06/07 STUKI絞りを追加
    rstrbf.append("  and ck.stuki  = ");
    rstrbf.append("       (select pk.stuki from paykbn pk ");
    rstrbf.append("                 where pk.kyuyo_kb = 100");
    rstrbf.append("                   and pk.pay_kb =   10" );
    rstrbf.append("       ) ");
    //<<<<<<<<<<<<<<<2012/06/07 STUKI絞りを追加
    rstrbf.append("  group by  ck.kyuyo_kb, ck.pay_kb, ck.sya_id, ck.kmk_id, ck.stuki");
    String sql = rstrbf.toString();
    return new String[]{sql};   }


    private final static String getSqlUpdate() {
              return "    UPDATE sonpo2 "
                      + "              SET "
                      + "                    stop_flg    = 21 ";
    //                  + "              WHERE hikifrom_tuki <= ? "
    //                  + "              AND   hikito_tuki   >= ? "
    //                  + "              AND   ((haraikata = 1)  OR "
    //                  + "                     (haraikata = 5)) "
    //                  + "              AND   stop_flg  =  0 ";

//  StringBuilder sb = new StringBuilder(300);
//
//  sb.append("UPDATE hunin_hd hhd ");
//  sb.append(" SET   ".concat(" hhd.ky_hkyo_nt_kb = 1 "));
//  sb.append(" WHERE ".concat(" hhd.ky_hkyo_nt_kb = 2 "));
//  sb.append(" AND    exists (select pk.sya_id ");
//  sb.append("                  from paykojin pk ");
//  sb.append("                 where pk.sya_id = hhd.sya_id ");
//  sb.append("                   and pk.s_key  = ? ) ");
//
//  return sb.toString();

    }

    private final static Statement getStatement(final String selectSql){
    try {
        return CCJSqlParserUtil.parse(selectSql);
    } catch (JSQLParserException e) {
        e.printStackTrace();
        throw new RuntimeException("*** Please Pass Select Sql as a proper sql format ***" + e);
    }
    }

    private final static Select convertUpdateIntoSelect(final String updateSql){

    // get the sql as Update statement Objects
    Statement statement = getStatement(updateSql);

    // get the update tableName
    Table updateTable = ((Update)statement).getTables().get(0);

    // build the new Select statement Object
    Select newSelect   = SelectUtils.buildSelectFromTable(updateTable);

    // Set the update where condition into Select conditions
    ((PlainSelect)newSelect.getSelectBody()).setWhere(((Update)statement).getWhere());

    return newSelect;
    }

    private static final String getDeleteExample(){
    return "delete from stzeimst st where " +
        "st.zeinendo = 10 " +
        "and " +
        "EXISTS ( " +
        "select " +
        "   *  " +
        "from  " +
        "   jlnk_paysyain jp " +
        "where " +

           "   jp.pay_kb = 10 AND " +
           "  jp.lnk_syori_kb = 20 AND " +
           " jp.stuki = 30 AND " +
           " jp.lnkact_kb = 50 AND " +
           " jp.sub_kb = 20 ) ";
    }

    private static final String convertDeleteIntoSelect(final String deleteSql){

    // get the sql as Update statement Objects
    Statement statement = getStatement(deleteSql);

    Table deleteTable = ((Delete)statement).getTable();

    // build the new Select statement Object
    Select newSelect   = SelectUtils.buildSelectFromTable(deleteTable);

    // Set the update where condition into Select conditions
    ((PlainSelect)newSelect.getSelectBody()).setWhere(((Delete)statement).getWhere());

    return "";
    }

    private static final String convertInsertIntoSelect(final String insertSql) {

    // get the sql as Delete statement Objects
    Statement statement = getStatement(insertSql);

    // insert table name
    Table insertTable = ((Insert)statement).getTable();

    // build the new Select statement Object with insert table name
    Select newSelect = SelectUtils.buildSelectFromTable(insertTable);

    Select select = ((Insert) statement).getSelect();

    if (select == null)
        return newSelect.toString();


    System.out.println(select);

    return mergeTwoSelectIntoOne((PlainSelect) newSelect.getSelectBody(),
        (PlainSelect) select.getSelectBody());

    }

    private static final String mergeTwoSelectIntoOne(final PlainSelect pS1,
        final PlainSelect pS2) {
    // A result select
    PlainSelect result = new PlainSelect();
    // List of tables invloved in both sql
    List<Join> tableList = new ArrayList<>();
    // get where's
    Expression pS1Exp = pS1.getWhere();
    Expression pS2Exp = pS2.getWhere();


    // from item for pS1 , -> we have to set this as a from item in our
    // final result
    FromItem pS1FromItem = pS1.getFromItem();

    // prepare joins item -> add all to tableList
    List<Join> pS1OtherTables = pS1.getJoins();
    if (CollectionUtils.isNotEmpty(pS1OtherTables))
        tableList.addAll(pS1OtherTables);

    // from item for pS2
    // change Ps2 fromItem into Join
    Join pS2Join = getJoinsFromTable(pS2.getFromItem());
    tableList.add(pS2Join);

    // joins for pS2
    List<Join> pS2OtherTables = pS2.getJoins();
    tableList.addAll(pS2OtherTables);

    // now prepare a new select
    result.addSelectItems(new AllColumns()); // add * as a select column
    result.setFromItem(pS1FromItem); // a from table
    result.setJoins(tableList); // join other tables

    // make sure the exp
    if (pS1Exp == null)
        result.setWhere(pS2Exp);
    else if (pS2Exp == null)
        result.setWhere(pS1Exp);
    else if (pS1Exp != null && pS2Exp != null) {
        AndExpression exp = new AndExpression(pS1Exp, pS2Exp);
        result.setWhere(exp);
    }

    System.out.println(result);


    return result.toString();
    }

    private static final Join getJoinsFromTable(final FromItem fromItem) {
    Join join = new Join();
    join.setRightItem(fromItem);
    join.setSimple(true);
    return join;
    }

    private static final List<Table> changeJoinToTable(final List<Join> joins){
    return joins.isEmpty() ? Collections.emptyList() : joins.stream()
        .map(j -> (Table) j.getRightItem())
        .collect(Collectors.toList());
    }

    private static String getInsertSql() {
//   StringBuilder sb = new StringBuilder();
//
//   sb.append("INSERT INTO  kgky_wk_taisyo_syain ");
//   sb.append("    ( kyuyo_kb");
//   sb.append("     ,pay_kb");
//   sb.append("     ,sya_id");
//   sb.append("     ,pr_kyuyo_kb");
//   sb.append("     ,fstuki");
//   sb.append("     ,tstuki )");
//   sb.append(" SELECT ");
//   sb.append("       ?");
//   sb.append("      ,?");
//   sb.append("      ,hhd.sya_id");
//   sb.append("      ,1");
//   sb.append("      ,min(hhd.hikyo_ky_fkj_stuki)");
//   sb.append("      ,max(hhd.hikyo_ky_tkj_stuki) ");
//   sb.append("  FROM hunin_hd hhd ");
//   sb.append("      ,paykojin pk ");
//   sb.append(" WHERE hhd.hikyo_ky_fkj_stuki > 0 ");
//   sb.append("   AND hhd.hikyo_ky_tkj_stuki > 0 ");
//   // TODO 給与側だけこのwhere句がない
//   // sb.append("AND    hhd.tokou_id > 0");
//   sb.append("   AND ( hhd.ky_hkyo_nt_kb = 1 OR hhd.ky_hkyo_kj_kb = 1 ) ");
//   sb.append("   AND NOT EXISTS ");
//   sb.append("       ( SELECT ks.sya_id ");
//   sb.append("           FROM kgky_syotoku_chosei ks ");
//   sb.append("          WHERE ks.skyuyo_kb = ? ");
//   sb.append("            AND ks.spay_kb  <> ? ");
//   sb.append("            AND pk.sya_id    = hhd.sya_id ");
//   sb.append("            AND pk.sya_id    = ks.sya_id  ) ");
//   sb.append("   AND pk.sya_id = hhd.sya_id ");
//   sb.append("   AND pk.s_key  = ? ");
//   sb.append(" GROUP BY  hhd.sya_id ");
//
//   return sb.toString();

    StringBuilder sb = new StringBuilder();
    sb.append("INSERT INTO  kgky_wk_taisyo_kikan ");
    sb.append("    ( skyuyo_kb");
    sb.append("     ,spay_kb");
    sb.append("     ,sya_id");
    sb.append("     ,min_date");
    sb.append("     ,max_date");
    sb.append("    ) VALUES (?,?,?,?,?)");
    return sb.toString();
    }

    private final static String differIn() {
    final String SQL = "  SELECT jd.kytbl_no                    "
        + "        ,jd.kyfld_id                    "
        + "        ,jd.kyfld_type                  "
        + "        ,jd.lnksiki_no                  "
        + "        ,jd.lnkptn_no                   "
        + "        ,jd.lnktbl_no01                 "
        + "        ,jd.lnktbl_no02                 "
        + "        ,jd.lnktbl_no03                 "
        + "        ,jd.lnktbl_no04                 "
        + "        ,jd.lnktbl_no05                 "
        + "        ,jd.lnktbl_no06                 "
        + "        ,jd.lnktbl_no07                 "
        + "        ,jd.lnktbl_no08                 "
        + "        ,jd.lnktbl_no09                 "
        + "        ,jd.lnktbl_no10                 "
        + "        ,jd.lnkfld_id01                 "
        + "        ,jd.lnkfld_id02                 "
        + "        ,jd.lnkfld_id03                 "
        + "        ,jd.lnkfld_id04                 "
        + "        ,jd.lnkfld_id05                 "
        + "        ,jd.lnkfld_id06                 "
        + "        ,jd.lnkfld_id07                 "
        + "        ,jd.lnkfld_id08                 "
        + "        ,jd.lnkfld_id09                 "
        + "        ,jd.lnkfld_id10                 "
        + "        ,jd.lnkfld_type01               "
        + "        ,jd.lnkfld_type02               "
        + "        ,jd.lnkfld_type03               "
        + "        ,jd.lnkfld_type04               "
        + "        ,jd.lnkfld_type05               "
        + "        ,jd.lnkfld_type06               "
        + "        ,jd.lnkfld_type07               "
        + "        ,jd.lnkfld_type08               "
        + "        ,jd.lnkfld_type09               "
        + "        ,jd.lnkfld_type10               "
        + "        ,jd.lnkprm01                    "
        + "        ,jd.lnkprm02                    "
        + "        ,jd.lnkprm03                    "
        + "        ,jd.lnkprm04                    "
        + "        ,jd.lnkprm05                    "
        + "        ,jd.lnkprm06                    "
        + "        ,jd.lnkprm07                    "
        + "        ,jd.lnkprm08                    "
        + "        ,jd.lnkprm09                    "
        + "        ,jd.lnkprm10                    "
        + "        ,jd.kk_sub_use_kb01             "
        + "        ,jd.kk_sub_use_kb02             "
        + "        ,jd.kk_sub_use_kb03             "
        + "        ,jd.kk_sub_use_kb04             "
        + "        ,jd.kk_sub_use_kb05             "
        + "        ,jd.kk_sub_use_kb06             "
        + "        ,jd.kk_sub_use_kb07             "
        + "        ,jd.kk_sub_use_kb08             "
        + "        ,jd.kk_sub_use_kb09             "
        + "        ,jd.kk_sub_use_kb10             "
        + "        ,jd.kk_calcpt_cd01              "
        + "        ,jd.kk_calcpt_cd02              "
        + "        ,jd.kk_calcpt_cd03              "
        + "        ,jd.kk_calcpt_cd04              "
        + "        ,jd.kk_calcpt_cd05              "
        + "        ,jd.kk_calcpt_cd06              "
        + "        ,jd.kk_calcpt_cd07              "
        + "        ,jd.kk_calcpt_cd08              "
        + "        ,jd.kk_calcpt_cd09              "
        + "        ,jd.kk_calcpt_cd10              "
        + "        ,jd.kikan_kj_kb01               "
        + "        ,jd.kikan_kj_kb02               "
        + "        ,jd.kikan_kj_kb03               "
        + "        ,jd.kikan_kj_kb04               "
        + "        ,jd.kikan_kj_kb05               "
        + "        ,jd.kikan_kj_kb06               "
        + "        ,jd.kikan_kj_kb07               "
        + "        ,jd.kikan_kj_kb08               "
        + "        ,jd.kikan_kj_kb09               "
        + "        ,jd.kikan_kj_kb10               "
        + "        ,jd.kikan_aj_kb01               "
        + "        ,jd.kikan_aj_kb02               "
        + "        ,jd.kikan_aj_kb03               "
        + "        ,jd.kikan_aj_kb04               "
        + "        ,jd.kikan_aj_kb05               "
        + "        ,jd.kikan_aj_kb06               "
        + "        ,jd.kikan_aj_kb07               "
        + "        ,jd.kikan_aj_kb08               "
        + "        ,jd.kikan_aj_kb09               "
        + "        ,jd.kikan_aj_kb10               "
        + "        ,jd.kikan_mm01                  "
        + "        ,jd.kikan_mm02                  "
        + "        ,jd.kikan_mm03                  "
        + "        ,jd.kikan_mm04                  "
        + "        ,jd.kikan_mm05                  "
        + "        ,jd.kikan_mm06                  "
        + "        ,jd.kikan_mm07                  "
        + "        ,jd.kikan_mm08                  "
        + "        ,jd.kikan_mm09                  "
        + "        ,jd.kikan_mm10                  "
        + "        ,jd.kikan_dd01                  "
        + "        ,jd.kikan_dd02                  "
        + "        ,jd.kikan_dd03                  "
        + "        ,jd.kikan_dd04                  "
        + "        ,jd.kikan_dd05                  "
        + "        ,jd.kikan_dd06                  "
        + "        ,jd.kikan_dd07                  "
        + "        ,jd.kikan_dd08                  "
        + "        ,jd.kikan_dd09                  "
        + "        ,jd.kikan_dd10                  "
        + "        ,jd.year_cal_kb01               "
        + "        ,jd.year_cal_kb02               "
        + "        ,jd.year_cal_kb03               "
        + "        ,jd.year_cal_kb04               "
        + "        ,jd.year_cal_kb05               "
        + "        ,jd.year_cal_kb06               "
        + "        ,jd.year_cal_kb07               "
        + "        ,jd.year_cal_kb08               "
        + "        ,jd.year_cal_kb09               "
        + "        ,jd.year_cal_kb10               "
        + "        ,jd.kikan_cal_kb01              "
        + "        ,jd.kikan_cal_kb02              "
        + "        ,jd.kikan_cal_kb03              "
        + "        ,jd.kikan_cal_kb04              "
        + "        ,jd.kikan_cal_kb05              "
        + "        ,jd.kikan_cal_kb06              "
        + "        ,jd.kikan_cal_kb07              "
        + "        ,jd.kikan_cal_kb08              "
        + "        ,jd.kikan_cal_kb09              "
        + "        ,jd.kikan_cal_kb10              "
        + "        ,jd.nen                         "
        + "        ,jd.ki                          "
        + "        ,jd.jks_cd                      "
        + "        ,jd.jksyb_cd                    "
        + "        ,jd.cd_unmatch_kb               "
        + "        ,jd.cd_unmatch_val              "
        + "        ,jd.kzlnk_max_chk               "
        + "        ,jd.kzlnk_max_val               "
        + "        ,jd.ddate_kjdt_kb               "
        + "        ,jd.ddate_kjaj_kb               "
        + "        ,jd.ddate_kjmm                  "
        + "        ,jd.ddate_kjdd                  "
        + "        ,jd.ddate_yrcal_kb              "
        + "        ,jd.ddate_kjdt_cmp              "
        + "        ,jp.lnkkmk_kb                   "
        + "        ,jp.code_fg                     "
        + "    FROM jlnkdcltbl  jd                 "
        + "        ,jlnkptn     jp                 "
        + "   WHERE jd.pay_kb      =  ?            "
        + "     AND jd.sub_kb      =  ?            "
        + "     AND jd.lnkptn_no   <  90000        "
        + "     AND jd.lnkptn_no   =  jp.lnkptn_no "
        + "     AND (jd.kytbl_no,                      "
        + "          jd.kyfld_id,                      "
        + "          jd.lnksiki_no"
        + "         )                                  "
        + "          IN                                "
        + "         (SELECT                            "
        + "            jk.kytbl_no,                    "
        + "            jk.kyfld_id,                    "
        + "            jk.lnksiki_no                   "
        + "          FROM                              "
        + "            jlnkkjwktbl  jk                 "
        + "          WHERE                             "
        + "               jk.pay_kb     = ?            "
        + "          AND  jk.sub_kb     = ?            "
        + "          AND  jk.par_id     = ?            "
        + "          AND  jk.kytbl_no   = jd.kytbl_no  "
        + "          AND  jk.kyfld_id   = jd.kyfld_id  "
        + "          AND  jk.lnksiki_no = jd.lnksiki_no"
        + "         )                                  "
        + "ORDER BY jd.kytbl_no                    "
        + "        ,jd.kyfld_id                    "
        + "        ,jd.lnksiki_no                  ";
    return SQL;
    }

    private static String getSpl1() {
    return " SELECT k1.flg,k2.flg, k3.flg, k4.flg"
        + " FROM ky_flg_tbl k1,ky_flg_tbl k2, ky_flg_tbl k3, ky_flg_tbl k4"
        + " WHERE k1.flg_id = 'jlnk_tykn_kb'"
        + " AND   k2.flg_id = 'jlnk_knm_kb'"
        + " AND   k3.flg_id = 'jlnk_hat_tais_kb'"
        + " AND   k4.flg_id = 'jlnk_ktigai_befkai'";
    }

    private static final String bugId1901(){
    StringBuilder sb = new StringBuilder();
        sb.append("SELECT  ck.skdate");
        sb.append("       ,ck.kazei");
        sb.append("       ,ck.syotoku");
        sb.append("  FROM  ck_kihon ck");
        sb.append(" WHERE  ck.kyuyo_kb = 1 ");
        sb.append("   AND  ck.sya_id   = ? ");
        sb.append("   AND  ck.skdate BETWEEN ? AND ? ");
        sb.append(" UNION ");
        sb.append("SELECT  ck.skdate");
        sb.append("       ,ck.kazei");
        sb.append("       ,ck.syotoku");
        sb.append("  FROM  rui_ck_kihon ck");
        sb.append(" WHERE  ck.kyuyo_kb = 1 ");
        sb.append("   AND  ck.sya_id   = ? ");
        sb.append("   AND  ck.skdate BETWEEN ? AND ? ");
        return sb.toString();
    }
    
    private static final String bugId1900(){
//  return
//          " SELECT  lnkkjptn_no"+
//          "        ,lnkkj_kb_jn"+
//          "        ,lnkkj_kb_ky"+
//          "        ,jn_tbl_kb"+
//          "        ,lnkterm_kb_g"+
//          "        ,lnkterm_aj_g"+
//          "        ,lnkterm_dd_g"+
//          "        ,lnkkjdate_jn"+
//          "        ,lnkkjdate_ky"+
//          "        ,slide_use_kb"+
//          "        ,slide_jn_edate"+
//          "        ,slide_ky_edate"+
//          " FROM    jlnkkjptn"+
//          " WHERE   pay_kb        =  1"+
//          " AND     sub_kb        =  2"+
//          " AND   ("+
//          "         (      ( length(lnkkjdate_jn) >= 1 )"+
//          "           AND  ( length(lnkkjdate_ky) >= 1 )"+
//          "           AND  lnkkj_kb_jn  in ( 1, 2, 3, 4 )"+
//          "           AND  lnkkj_kb_ky  in ( 1, 2, 3, 4 )"+
//          "         )"+
//          "        OR"+
//          "         (      ( length(lnkkjdate_jn) >= 1 )"+
//          "           AND  ( length(lnkkjdate_ky) >= 1 )"+
//          "           AND  lnkkj_kb_jn  in ( 5 )"+
//          "           AND  lnkkj_kb_ky  in ( 1, 2, 3, 4, 5 )"+
//          "         )"+
//          "       )" +
//          " ORDER BY 1";
    
    return
            " SELECT  lnkkjptn_no"+
            "        ,lnkkj_kb_jn"+
            "        ,lnkkj_kb_ky"+
            "        ,jn_tbl_kb"+
            "        ,lnkterm_kb_g"+
            "        ,lnkterm_aj_g"+
            "        ,lnkterm_dd_g"+
            "        ,lnkkjdate_jn"+
            "        ,lnkkjdate_ky"+
            "        ,slide_use_kb"+
            "        ,slide_jn_edate"+
            "        ,slide_ky_edate"+
            " FROM    jlnkkjptn"+
            " WHERE   pay_kb        =  1"+
            " AND     sub_kb        =  2"+
            " AND   ("+
            "         (      ( length(lnkkjdate_jn) >= 1 )"+
            "           AND  ( length(lnkkjdate_ky) >= 1 )"+
            "         )"+
            "       )" +
            " ORDER BY 1";
    }
    
    private final static String getInsertOfSusi(){

       return  "INSERT INTO ab_check_res    "
               + "(kyuyo_kb,           pay_kb,    "
               + "  abchk_ptn,     sya_id,       kmk_id,   "
               + "    bf_kingaku,      af_kingaku,    sagaku,   "
               + "  prc_date)       VALUES        "
               + " (1,   10,  100,    1,   4,  "
               + " 30, 34,38,2017/03/16 11:49:27) where prc_date = '2010/10/10'"; 

    }


}

