# java调用存储过程

#### java调用带有return 返回值的写法
##### 存储过程例子
```
CREATE OR REPLACE FUNCTION FC_seq_result(p_seqname IN VARCHAR2,
                                         p_type    IN NUMBER) RETURN NUMBER IS
  v_seq    VARCHAR2(1024);
  v_pro    VARCHAR2(1024);
  v_result NUMBER(10);
BEGIN
  IF p_type = 1 THEN
    v_pro := '.NEXTVAL';
  ELSIF p_type = 0 THEN
    v_pro := '.CURRVAL';
  END IF;
  v_seq := 'SELECT ' || p_seqname || v_pro || ' FROM DUAL';
  EXECUTE IMMEDIATE V_SEQ
    INTO V_RESULT;
  Return(v_result);
END;
```
##### 存储过程调用
```
/***
	 * 获取Seq值，type为1获取nextval;type为0获取currval
	 * 
	 * @param sqNm
	 * @param type
	 * @return
	 * @throws Exception
	 */
	public String getSquenceValue(String sqNm, int type) throws Exception {
		Connection conn = null;
		CallableStatement pstmt= null;
		ResultSet rs = null;
		try {
			conn = SessionFactoryUtils.getDataSource(getSessionFactory()).getConnection();
			pstmt = conn.prepareCall("{? = call FC_seq_result(?, ?)}");
			pstmt.setString(2, sqNm);
			pstmt.setInt(3, type);
			pstmt.registerOutParameter(1, Types.NUMERIC);
			//这块判断了是什么数据库，不过没测试不知道这块的写法对不对 lmd
			if ("sqlserver".equals(getDbType())) {
				rs = pstmt.executeQuery();
				while (rs.next()) {
					return rs.getString(1);
				}
				throw new Exception("获取Sequence失败,[" + sqNm + "]");
			} else {
				pstmt.executeUpdate();
				Object obj = pstmt.getObject(1);
				if (null != obj && !"".equals(obj.toString())) {
					String seqVal = obj.toString();
					return seqVal;
				}
			}
		} catch (Exception e) {
			throw new Exception("获取Sequence失败,[" + sqNm + "]");
		} finally {
			if (null != rs) rs.close();
			if (null != pstmt) pstmt.close();
			if (null != conn) conn.close();
		}
		throw new Exception("获取Sequence失败,[" + sqNm + "]");
	}

```