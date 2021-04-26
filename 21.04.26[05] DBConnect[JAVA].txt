package martcon;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConnect {

	public static void main(String[] args) {

		Connection con = null;
		try {
			con = DBCon.getCon();
			System.out.println("DB 연결 성공!!!");
		} catch (Exception e) {
			System.out.println("DB 연결 오류...");
			e.printStackTrace();
		}

	}

}
