package martcon;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Scanner;
import java.util.Date;

public class MartAdm {
	
	public Scanner scanner = new Scanner(System.in);
	public int inputInt() {return scanner.nextInt();}
	public String inputStr() {return scanner.next();}

	public static void main(String[] args) throws Exception {

		MartAdm ma = new MartAdm();
		ma.menu();

	}
	
	public void menu() throws Exception {
		
		System.out.println("\n MartCon");
		System.out.println("===========================");
	      System.out.println(" [1] 상품 입고 처리");
	      System.out.println(" [2] 상품 출고 처리");
	      System.out.println(" [3] 상품 목록 제거");
	      System.out.println(" [4] 상품 재고 조회");
	      System.out.println(" [5] 전체 상품 목록 조회");
	      System.out.println(" [6] 전체 회원 목록 조회");
	      System.out.println(" [7] 회원 아이디 찾기");
	      System.out.println(" [8] 프로그램 종료");
	      System.out.println("===========================");
	      System.out.println(" Select Menu : ");
	      
	      int selectNo = inputInt();
	      
	      switch(selectNo){
	         case 1:
	            System.out.println("상품 입고 처리");
	            addGds();
	            break;
	         case 2:
	            System.out.println("상품 출고 처리");
	            subGds();
	            break;
	         case 3:
	            System.out.println("상품 목록 제거");
	            delGds();
	            break;
	         case 4:
	            System.out.println("상품 재고 조회");
	            printGds();
	            break;
	         case 5:
	            System.out.println("전체 상품 목록 조회");
	            printAll();
	            break;
	         case 6:
		            System.out.println("전체 회원 목록 조회");
		            printCustAll();
		            break;
	         case 7:
		            System.out.println("회원 아이디 찾기");
		            searchID();
		            break;
	         case 8:
	            System.out.println("프로그램을 종료합니다.");
	            System.exit(0);
	            break;
	                  }
	      menu();

	}
	
	private void addGds() throws Exception {
		
		System.out.println("\n상품코드를 입력하세요: ");
	      String Gds_cd = inputStr();
	      TR_GDS ma = readDB(Gds_cd);
	      
	      if(ma != null) {
	         System.out.print(ma.getGds_nm()+"의 추가 입고 수량을 입력하세요: ");
	         int inCnt = inputInt();
	         int cnt = ma.getGds_cnt() + inCnt;
	         updateDB(Gds_cd,cnt);
	         System.out.println(ma.getGds_nm() + "의 입고 후 수량 : " + cnt);
	      }
	      else {
	    	  System.out.print("상품명을 입력하세요: ");
	    	  String Gds_nm = inputStr();
	    	  System.out.print("상품가격을 입력하세요: ");
	    	  int Sale_amt = inputInt();
	    	  System.out.print("입고 수량을 입력하세요: ");
	    	  int Gds_cnt = inputInt();
	    	  System.out.print("등록 일자를 입력하세요: ");
	    	  String Rdg_dt = inputStr();
	    	  
	    	  ma = new TR_GDS();
	    	  
	    	  ma.setGds_cd(Gds_cd);
	    	  ma.setGds_nm(Gds_nm);
	    	  ma.setSale_amt(Sale_amt);
	    	  ma.setGds_cnt(Gds_cnt);
	    	  ma.setRdg_dt(Rdg_dt);
	    	  insertMA(ma);    	  
	    	  
	      }
	}
	
	private TR_GDS readDB(String gds_cd) throws Exception {

		TR_GDS ma = new TR_GDS();
	    Connection con = DBCon.getCon();
	      
	    String sql = "select * from TR_GDS where Gds_cd = '" + gds_cd + "'";
	    Statement stmt = con.createStatement();
	    ResultSet rs = stmt.executeQuery(sql);
	      
	    if(!rs.next()) {
	       ma = null;
	    }
	    else {
	       ma.setGds_cd(rs.getString(1));
	       ma.setGds_nm(rs.getString(2));
	       ma.setSale_amt(rs.getInt(3));
	       ma.setGds_cnt(rs.getInt(4));
	       ma.setRdg_dt(rs.getString(5));
	    }
	    DBCon.close(con, stmt, rs);
	    
	    return(ma);
	}
	
	private void updateDB(String gds_cd, int gds_cnt) throws Exception {

		Connection con = DBCon.getCon();
	      
	      String sql = "update TR_GDS set Gds_cnt = "+gds_cnt+" where Gds_cd = '"+gds_cd+"'";
	      Statement stmt = con.createStatement();
	      stmt.executeUpdate(sql);
	      DBCon.close(con, stmt, null);
		
	}	

	private void insertMA(TR_GDS ma) throws Exception {

		Connection con = DBCon.getCon();
		
		String sql = "insert into TR_GDS(Gds_cd, Gds_nm, Sale_amt, Gds_cnt, Reg_dt)"
				+" values('" + ma.getGds_cd() + "','" + ma.getGds_nm() +
				"'," + ma.getSale_amt() + "," + ma.getGds_cnt() + ",'" + ma.getReg_dt() +"')";
		Statement stmt = con.createStatement();
		stmt.executeUpdate(sql);
		DBCon.close(con, stmt, null);
		
	}
		
	private void subGds() throws Exception {

		System.out.println("상품코드를 입력하세요 : ");
		String Gds_cd = inputStr();
		TR_GDS ma = readDB(Gds_cd);
		if(ma != null) {
			System.out.println("출고 수량을 입력하세요 : ");
			int inCnt = inputInt();
			int cnt = ma.getGds_cnt() - inCnt;
			if(cnt >= 0) {
				updateDB(Gds_cd, cnt);
				System.out.println(ma.getGds_nm()+"의 출고 후 수량 : " +cnt );
			}
			else {
				System.out.println("출고 수량이 재고 수량보다 많습니다.");
			}
		}
		
	}

	private void delGds() throws Exception {
		
		System.out.println("상품코드를 입력하세요 : ");
		String Gds_cd = inputStr();
		TR_GDS ma = readDB(Gds_cd);
		if(ma != null) {
			deleteDB(Gds_cd);
			System.out.println(ma.getGds_nm()+"(이)가 상품 목록에서 제거되었습니다.");
		}
		
	}
	
	private void deleteDB(String gds_cd) throws Exception {
		
		Connection con = DBCon.getCon();

		String sql = "delete TR_GDS where Gds_cd = '" + gds_cd + "'";
		Statement stmt = con.createStatement();
		stmt.executeUpdate(sql);
		DBCon.close(con, stmt, null);
		
	}
	
	private void printGds() throws Exception {

		System.out.println("상품코드를 입력하세요 : ");
		String Gds_cd = inputStr();
		TR_GDS ma = readDB(Gds_cd);
		if(ma != null) {
			titlePrint();
			martInfo(ma);
			
		}
		
	}
	
	private void printAll() throws Exception {
		
		titlePrint();
		
		Connection con = DBCon.getCon();
		TR_GDS ma = new TR_GDS();
		String sql = "select * from TR_GDS";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		while(rs.next()) {
			ma.setGds_cd(rs.getString(1));
		    ma.setGds_nm(rs.getString(2));
		    ma.setSale_amt(rs.getInt(3));
		    ma.setGds_cnt(rs.getInt(4));
		    ma.setRdg_dt(rs.getString(5));
		    martInfo(ma);
		}
		
		DBCon.close(con, stmt, rs);
		System.out.println("\n");
		
	}
	
	private void printCustAll() throws Exception {
		
		titleCustPrint();
		
		Connection con = DBCon.getCon();
		TR_BUYER mc = new TR_BUYER();
		String sql = "select * from TR_BUYER";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		while(rs.next()) {
			mc.setMshp_id(rs.getString(1));
		    mc.setMshp_pw(rs.getString(2));
		    mc.setMshp_name(rs.getString(3));
		    custInfo(mc);
		}
		
		DBCon.close(con, stmt, rs);
		System.out.println("\n");
		
	}
	
	private void searchID() throws Exception {
		
			System.out.println("이름을 입력하세요 : ");
			String Mshp_name = inputStr();
			TR_BUYER mc = readDBby(Mshp_name);
			if(mc != null) {
				titleCustPrint();
				custInfo(mc);
			}
	}

	private TR_BUYER readDBby(String Mshp_name) throws Exception {

		TR_BUYER mc = new TR_BUYER();
		Connection con = DBCon.getCon();
      
		String sql = "select * from TR_BUYER where Mshp_name = '" + Mshp_name + "'";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
      
		if(!rs.next()) {
			mc = null;
		}
		else {
			mc.setMshp_id(rs.getString(1));
			mc.setMshp_pw(rs.getString(2));
			mc.setMshp_name(rs.getString(3));
		}
		DBCon.close(con, stmt, rs);
    
		return(mc);
	}
	
	public void titlePrint() {
		System.out.println("\n");
		System.out.println("\n\t\t상 품 정 보");
		System.out.println("===================================");
		System.out.println("상품코드\t 상품명\t 가격 \t재고 \t등록일자");
		System.out.println("===================================");
	}

	public void martInfo(TR_GDS ma) {
		System.out.print(ma.getGds_cd()+"\t");
		System.out.print(ma.getGds_nm()+"\t");
		System.out.print(ma.getSale_amt()+"\t");
		System.out.print(ma.getGds_cnt()+"\t");
		System.out.print(ma.getReg_dt()+"\t");
		System.out.println();
	}
	
	public void titleCustPrint() {
		System.out.println("\n");
		System.out.println("\n\t회 원 정 보");
		System.out.println("=======================");
		System.out.println("아이디\t 비밀번호\t 이름");
		System.out.println("=======================");
	}

	public void custInfo(TR_BUYER mc) {
		System.out.print(mc.getMshp_id()+"\t");
		System.out.print(mc.getMshp_pw()+"\t");
		System.out.print(mc.getMshp_name()+"\t");
		System.out.println();
	}	
}
