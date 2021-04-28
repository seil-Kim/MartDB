package martcon;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Scanner;

public class MartCust {
	
	public Scanner scanner = new Scanner(System.in);
	public int inputInt() {return scanner.nextInt();}
	public String inputStr() {return scanner.next();}

	public static void main(String[] args) throws Exception{

		MartCust mc = new MartCust();
		mc.menu();
		
	}
	
	public void menu() throws Exception {
		TR_BUYER Buyer = new TR_BUYER();
		Buyer = null;
		while(true) {
		
			System.out.println("\n MartCon");
			System.out.println("==============================");
			System.out.println("[1] 회원 가입");
			System.out.println("[2] 로그인");
			System.out.println("[3] 로그아웃");
			System.out.println("[4] 상품 목록 및 장바구니 담기");
			System.out.println("[5] 상품 결제");
			System.out.println("[6] 프로그램 종료");
			System.out.println("==============================");
			System.out.println("Select Menu : ");
		
			String selecNo = inputStr();
		
			switch(selecNo) {
			case "1":
				System.out.println("회원 가입");
				addCust();
				break;
			case "2":
				System.out.println("로그인");
				Buyer = loginSystem();
				break;
			case "3":
				System.out.println("로그아웃");
				Buyer = logoutSystem(Buyer);
				break;
			case "4":
				System.out.println("상품 목록 및 장바구니 담기");
				buyGds(Buyer);
				break;
			case "5":
				System.out.println("상품 결제");
//    			buyGds();
				break;
			case "6":
				System.out.println("프로그램을 종료합니다.");
				System.exit(0);
				break;
			}
		}
	}
	
	public void addCust() throws Exception {
	
		System.out.print("\nID를 입력하세요: ");
		String mshp_id = inputStr();
		String mshp_pw = null, mshp_pw2 = null;
		boolean result = searchId(mshp_id);
		if(result) {
			System.out.println("이미 가입된 계정 입니다.");
			addCust();
		}else {
			do {
				System.out.print("PW를 입력하세요: ");
				mshp_pw = inputStr();
				System.out.print("PW를 재입력하세요: ");
				mshp_pw2 = inputStr();
			}while(!mshp_pw.equals(mshp_pw2));

		}

		System.out.print("이름을 입력하세요: ");
		String mshp_name = inputStr();
		
		TR_BUYER mc = new TR_BUYER();
		
		mc.setMshp_id(mshp_id);
		mc.setMshp_pw(mshp_pw);
		mc.setMshp_name(mshp_name);
		insertMC(mc);
	}
	
	private boolean searchId(String mshp_id) throws Exception {

		boolean result = false;
		Connection con = DBCon.getCon();
		
		String sql = "select * from TR_BUYER where Mshp_id = '" + mshp_id +"'";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		if(rs.next())
			result = true;
		else
			result = false;
		DBCon.close(con, stmt, rs);
		
		return(result);
	}
	
	private void insertMC(TR_BUYER mc) throws Exception {

		Connection con = DBCon.getCon();

		String sql = "insert into TR_BUYER(Mshp_id, Mshp_pw, Mshp_name)"
				+ " values('" + mc.getMshp_id() + "','" + mc.getMshp_pw() + "','"
				+ mc.getMshp_name() + "')";
		
		Statement stmt = con.createStatement();
		stmt.executeUpdate(sql);
		DBCon.close(con, stmt, null);
		
	}
	
	private TR_BUYER loginSystem() throws Exception {
		
			TR_BUYER Buyer = login_id_pw();

			return Buyer;
		
	}
	
	private TR_BUYER login_id_pw() throws Exception {		

		System.out.print("아이디를 입력하세요 : ");
		String Mshp_id = inputStr();
		System.out.print("비밀번호를 입력하세요 : ");
		String Mshp_pw = inputStr();
		
		TR_BUYER Buyer = new TR_BUYER();
		
		Connection con = DBCon.getCon();
		
		String sql = "select * from TR_BUYER where Mshp_id = '" + Mshp_id +
				"' and Mshp_pw = '" + Mshp_pw + "'";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		if(!rs.next()) {
			System.out.println("해당 ID나 PW가 일치하지 않습니다.");
		}else {
						
			Buyer.setMshp_id(rs.getString(1));
			Buyer.setMshp_pw(rs.getString(2));
			Buyer.setMshp_name(rs.getString(3));
			
			System.out.println(Buyer.getMshp_name() + "님 로그인을 성공하였습니다.");
		}
				
	return Buyer;
}
	

	private TR_BUYER logoutSystem(TR_BUYER Buyer) {

		System.out.println(Buyer.getMshp_name() + "님 로그아웃 되었습니다.");
		Buyer = null;
		
		return Buyer;
		
	}
	
	private void buyGds(TR_BUYER Buyer) throws Exception {
		
		if(Buyer != null) {
			readGDS();
			
			System.out.print("장바구니에 담을 상품명을 입력하세요 : ");
			String gds_nm = inputStr();
			boolean result = searchBG(gds_nm);
			if(result){
				System.out.println("장바구니에 담겨져있습니다. 수량을 추가하시겠습니까?(y,n)");
			}else {
				System.out.print("수량을 입력하세요 : ");
				int gds_buycnt = inputInt();
				
				TR_GDSLIST gl = new TR_GDSLIST();
				
				gl.setGds_nm(gds_nm);
				gl.setGds_buycnt(gds_buycnt);
				insertGL(gl, Buyer);
				
			}		
			
		}else {
			System.out.println("로그인 먼저 해주세요.");
		}		
	}
	
	private boolean searchBG(String gds_nm) throws Exception {

		boolean result = false;
		Connection con = DBCon.getCon();
		
		String sql = "select * from TR_GDSLIST where Gds_nm = '" + gds_nm + "'";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		if(rs.next())
			result = true;
		else
			result = false;
		DBCon.close(con, stmt, rs);
		
		return(result);
	}
	private void insertGL(TR_GDSLIST gl, TR_BUYER Buyer) throws Exception {

		Connection con = DBCon.getCon();
		
		String sql = "insert into TR_GDSLIST(Mshp_id, Gds_nm, Gds_buycnt)"
				+ " values('" + Buyer.getMshp_id() + "','" + gl.getGds_nm() + "',"
				+ gl.getGds_buycnt() + ")";
		
		Statement stmt = con.createStatement();
		stmt.executeUpdate(sql);
		DBCon.close(con, stmt, null);		
		
	}
	private void readGDS() throws Exception {
		
		titlePrint();
		
		Connection con = DBCon.getCon();

		TR_GDS ma = new TR_GDS();
		String sql = "select Gds_nm, Sale_amt, Gds_cnt from TR_GDS";
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		
		while(rs.next()) {
	         ma.setGds_nm(rs.getString(1));
	         ma.setSale_amt(rs.getInt(2));
	         ma.setGds_cnt(rs.getInt(3));
	         martInfo(ma);
	         
	    }
		
	      DBCon.close(con, stmt, rs);
	      System.out.println("\n");
	      
	}
	
	public void titlePrint() {
		System.out.println("\n");
		System.out.println("\n\t상 품 정 보");
		System.out.println("=======================");
		System.out.println("상품명\t 가격 \t재고");
		System.out.println("=======================");
		
	}

	public void martInfo(TR_GDS ma) {
		System.out.print(ma.getGds_nm()+"\t");
		System.out.print(ma.getSale_amt()+"\t");
		System.out.print(ma.getGds_cnt()+"\t");
		System.out.println();
		
	}
	
}
