# Telephone-Diary-Applicationusing System;
using System.Data;
using System.Windows.Forms;
using System.Data.SQLite;

namespace telephonediary
{
    public partial class phone : Form
    {
        SQLiteConnection con = new SQLiteConnection(@"Data Source=C:\Users\mreece\OneDrive - CoStar Realty Information, Inc\Desktop\Web Scraping Tips-Tricks-Notes\phone.db;Version=3;");

        public phone()
        {
            InitializeComponent();
        }

        private void label5_Click(object sender, EventArgs e) { }

        private void phone_Load(object sender, EventArgs e)
        {
            Display();
        }

        private void bttNew_Click(object sender, EventArgs e)
        {
            txtFirstName.Clear();
            txtLastName.Clear();
            txtEmail.Clear();
            txtMobile.Clear();
            txtCategory.SelectedIndex = -1;
            txtFirstName.Focus();
        }

        private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e) { }

        private void bttInsert_Click(object sender, EventArgs e)
        {
            con.Open();
            using (SQLiteCommand cmd = new SQLiteCommand("INSERT INTO Mobiles (First, Last, Mobile, Email, Category) VALUES (@First, @Last, @Mobile, @Email, @Category)", con))
            {
                cmd.Parameters.AddWithValue("@First", txtFirstName.Text);
                cmd.Parameters.AddWithValue("@Last", txtLastName.Text);
                cmd.Parameters.AddWithValue("@Mobile", txtMobile.Text);
                cmd.Parameters.AddWithValue("@Email", txtEmail.Text);
                cmd.Parameters.AddWithValue("@Category", txtCategory.Text);

                cmd.ExecuteNonQuery();
            }
            con.Close();

            MessageBox.Show("Successfully saved...!!!");
            Display();
        }

        void Display()
        {
            con.Open();
            SQLiteDataAdapter sda = new SQLiteDataAdapter("Select * from Mobiles", con);
            DataTable dt = new DataTable();
            sda.Fill(dt);
            dataGridView1.Rows.Clear();

            foreach (DataRow item in dt.Rows)
            {
                int n = dataGridView1.Rows.Add();
                dataGridView1.Rows[n].Cells[0].Value = item["First"].ToString();
                dataGridView1.Rows[n].Cells[1].Value = item["Last"].ToString();
                dataGridView1.Rows[n].Cells[2].Value = item["Mobile"].ToString();
                dataGridView1.Rows[n].Cells[3].Value = item["Email"].ToString();
                dataGridView1.Rows[n].Cells[4].Value = item["Category"].ToString();
            }
            con.Close();
        }

        private void bttDelete_Click(object sender, EventArgs e)
        {
            con.Open();
            using (SQLiteCommand cmd = new SQLiteCommand("DELETE FROM Mobiles WHERE Mobile = @Mobile", con))
            {
                cmd.Parameters.AddWithValue("@Mobile", txtMobile.Text);
                int rowsAffected = cmd.ExecuteNonQuery();

                if (rowsAffected > 0)
                {
                    MessageBox.Show("Delete successfully...!!!");
                }
                else
                {
                    MessageBox.Show("No record found with the specified mobile number.");
                }
            }
            con.Close();

            Display();
        }

        private void dataGridView1_MouseClick(object sender, MouseEventArgs e)
        {
            if (dataGridView1.SelectedRows.Count > 0)
            {
                DataGridViewRow selectedRow = dataGridView1.SelectedRows[0];
                txtFirstName.Text = selectedRow.Cells[0].Value.ToString();
                txtLastName.Text = selectedRow.Cells[1].Value.ToString();
                txtMobile.Text = selectedRow.Cells[2].Value.ToString();
                txtEmail.Text = selectedRow.Cells[3].Value.ToString();
                txtCategory.Text = selectedRow.Cells[4].Value.ToString();
            }
        }

        private void bttUpdate_Click(object sender, EventArgs e)
        {
            con.Open();
            using (SQLiteCommand cmd = new SQLiteCommand("UPDATE Mobiles SET First = @First, Last = @Last, Email = @Email, Category = @Category WHERE Mobile = @Mobile", con))
            {
                cmd.Parameters.AddWithValue("@First", txtFirstName.Text);
                cmd.Parameters.AddWithValue("@Last", txtLastName.Text);
                cmd.Parameters.AddWithValue("@Mobile", txtMobile.Text); // Assuming Mobile number is the unique identifier
                cmd.Parameters.AddWithValue("@Email", txtEmail.Text);
                cmd.Parameters.AddWithValue("@Category", txtCategory.Text);

                cmd.ExecuteNonQuery();
            }
            con.Close();

            MessageBox.Show("Update successfully...!!!");
            Display();
        }
    }
}
