## Simple SQL Getter, populate table

```C#
public DataTable GetDocuments(int userID, int personnelID, string category)
  {
    DataTable dt = new DataTable();
    string connStr = ConfigurationManager.ConnectionStrings["ITC_glostone"].ToString();

    using (SqlConnection conn = new SqlConnection(connStr))
    {
      conn.Open();
      using (SqlCommand cmd = new SqlCommand("[sp_CIC_Personnel_DocumentDQSelect]", conn) { CommandType = CommandType.StoredProcedure })
      {
        cmd.Parameters.AddWithValue("@UserID", userID);
        cmd.Parameters.AddWithValue("@PersonnelID", personnelID);
        cmd.Parameters.AddWithValue("@NavigateCategory", category);

        SqlDataAdapter _da = new SqlDataAdapter();
        _da.SelectCommand = cmd;
        _da.Fill(dt);
      }
      conn.Dispose();
    }
    return dt;
  }
