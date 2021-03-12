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
```


## Singleton, with reader
```C#
public PersonnelSingleModel GetPerson(int ID, int clientID, int userID)
{
    PersonnelSingleModel model = new PersonnelSingleModel(); 
    string connStr = ConfigurationManager.ConnectionStrings["ITC_glostone"].ToString();

    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        using (SqlCommand cmd = new SqlCommand("sp_CIC_Personnel_SelectSingle", conn) { CommandType = CommandType.StoredProcedure })
        {
            cmd.Parameters.AddWithValue("@ID", ID);
            cmd.Parameters.AddWithValue("@ClientID", clientID);
            cmd.Parameters.AddWithValue("@UserID", userID);

            SqlDataReader rdr = cmd.ExecuteReader();
            if (rdr.Read())
            {
                model.ID = (int)ID;
                model.ClientID = clientID;
                model.UserID = userID;
                model.FirstName = rdr["FirstName"].ToString();
                model.LastName = rdr["LastName"].ToString();
                model.PrimaryPhone = rdr["PrimaryPhone"].ToString();
                model.Email = rdr["Email"].ToString();
                model.JobTitleID = (int)rdr["JobTitleID"];
                model.DQValue = (decimal)rdr["DQValue"];
                model.CarrierDQValue = (decimal)rdr["CarrierDQValue"];
                model.LicenseNumber = rdr["LicenseNumber"].ToString();
                model.LicenseClass = rdr["LicenseClass"].ToString();
                model.LicenseState = rdr["LicenseState"].ToString();
                model.EmergencyContactNotify = rdr["EmergencyContactNotify"].ToString();
                model.EmergencyContactRelationship = rdr["EmergencyContactRelationship"].ToString();
                model.EmergencyContactPhone = rdr["EmergencyContactPhone"].ToString();
                model.EmergencyContactCity = rdr["EmergencyContactCity"].ToString();
                model.EmergencyContactStateOrProvinceID = (int)rdr["EmergencyContactStateOrProvinceID"];

                model.DateOfBirth = GetDateTimeFromReader(rdr["DateOfBirth"]);
                model.DateOfHire = GetDateTimeFromReader(rdr["DateOfHire"]);
                model.LicenseIssueDate = GetDateTimeFromReader(rdr["LicenseIssueDate"]);
                model.LicenseExpirationDate = GetDateTimeFromReader(rdr["LicenseExpirationDate"]);
            }
        }
        conn.Dispose();
    }

    return model;
}
    ```
