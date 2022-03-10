CHocVien.cs

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace WindowsFormsApp4
{
    [Serializable]
    class CHocVien
    {
        private string m_mahv;
        private string m_hoten;
        private DateTime m_ngaysinh = new DateTime();
        private bool m_gioitinh;
        private double m_diem1;
        private double m_diem2;

        public string mahv
        {
            get { return m_mahv; }
            set { m_mahv = value; }
        }
        public string hoTen
        {
            get { return m_hoten; }
            set { m_hoten = value; }
        }
        public DateTime ngaySinh
        {
            get { return m_ngaysinh; }
            set { m_ngaysinh = value; }
        }
        public bool gioiTinh
        {
            get { return m_gioitinh; }
            set { m_gioitinh = value; }
        }
        public double diem1
        {
            get { return m_diem1; }
            set { m_diem1 = value; }
        }
        public double diem2
        {
            get { return m_diem2; }
            set { m_diem2 = value; }
        }
        public CHocVien()
        {
            m_mahv = "";
            m_hoten = "";
            m_ngaysinh = DateTime.Today;
            m_gioitinh = false;
            m_diem1 = 0;
            m_diem2 = 0;
        }
        public CHocVien(string ma, string ten, DateTime ns, bool gt , double diem1, double diem2)
        {
            ma = m_mahv;
            ten = m_hoten;
            ns = m_ngaysinh;
            gt = m_gioitinh;
            diem1 = m_diem1;
            diem2 = m_diem2;
        }
    }
}





CTruyCapDuLieu.cs 

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Runtime.Serialization.Formatters.Binary;
using System.IO;

namespace WindowsFormsApp4
{
    [Serializable]
    class CTruyCapDuLieu
    {
        private static CTruyCapDuLieu instance = null;
        private List<CHocVien> dsHocVien;
        private CTruyCapDuLieu()
        {
            dsHocVien = new List<CHocVien>();
        }
        public static CTruyCapDuLieu khoiTao()
        {
            if (instance == null)
                instance = new CTruyCapDuLieu();
            return instance;
        }
        public List<CHocVien> getDSHocVien()
        {
            return dsHocVien;
        }
        public static bool ghiFile(string tenfile)
        {
            try
            {
                FileStream f = new FileStream(tenfile, FileMode.Create);
                if (f == null) return false;
                BinaryFormatter bf = new BinaryFormatter();
                bf.Serialize(f, instance);
                f.Close();
                return true;
            }
            catch (Exception)
            {
                return false;
            }
        }
        public static bool docFile(string tenfile)
        {
            try
            {
                FileStream f = new FileStream(tenfile, FileMode.Open);
                if (f == null) return false;
                BinaryFormatter bf = new BinaryFormatter();
                instance = bf.Deserialize(f) as CTruyCapDuLieu;
                f.Close();
                return true;
            }
            catch (Exception)
            {
                return false;
            }
        }
    }
}


CXuLyHV.cs

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace WindowsFormsApp4
{
    class CXuLyHV
    {
        private List<CHocVien> _dsHV;
        public CXuLyHV()
        {
            CTruyCapDuLieu duLieu = CTruyCapDuLieu.khoiTao();
            this._dsHV = duLieu.getDSHocVien();
        }
        public List<CHocVien> getDSHocVien()
        {
            return this._dsHV.ToList();
        }
        public CHocVien timHV(string mahv)
        {
            foreach (CHocVien hv in this._dsHV)
            {
                if(hv.mahv == mahv)
                {
                    return hv;
                }    
            }
            return null;
        }
        public void them(CHocVien hv)
        {
            if (timHV(hv.mahv) == null)
                this._dsHV.Add(hv);
            else
                MessageBox.Show("Mã Học viên đã tồn tại");
        }
        public void suaHV(CHocVien hv)
        {
            CHocVien kqTim = timHV(hv.mahv);
            if (kqTim != null)
            {
                kqTim.hoTen = hv.hoTen;
                kqTim.ngaySinh = hv.ngaySinh;
                kqTim.gioiTinh = hv.gioiTinh;
                kqTim.diem1 = hv.diem1;
                kqTim.diem2 = hv.diem2;
            }
            else
                MessageBox.Show("Không cho phép thay đổi mã học viên ");
        }
        public void xoaHV(String mahv)
        {
            CHocVien tim = timHV(mahv);
            if (tim != null)
                _dsHV.Remove(tim);
        }

    }
}



Form 

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace WindowsFormsApp4
{
    public partial class Form1 : Form
    {
        private CXuLyHV xuLyHV;
        public Form1()
        {
            InitializeComponent();
            xuLyHV = new CXuLyHV();
        }

        private void hienThiDSHV()
        {
            dgvHocVien.DataSource = xuLyHV.getDSHocVien();
            dgvHocVien.Refresh();
        }

        
        private void btnThem_Click(object sender, EventArgs e)
        {
            CHocVien hv = new CHocVien();
            hv.mahv = txtMaHV.Text;
            hv.hoTen = txtHoTen.Text;
            hv.ngaySinh = dtpNgaySinh.Value.Date;
            hv.gioiTinh = radNam.Checked;
            hv.diem1 = double.Parse(txtDiem1.Text);
            hv.diem2 = double.Parse(txtDiem2.Text);
            txtKetQua.Text = ((hv.diem1 + hv.diem2)/2).ToString("");


            xuLyHV.them(hv);
            hienThiDSHV();

        }


        private void Form1_Load(object sender, EventArgs e)
        {
            CTruyCapDuLieu.docFile("hv.dat");
            xuLyHV = new CXuLyHV();
            hienThiDSHV();
        }

        private void dgvHocVien_RowEnter(object sender, DataGridViewCellEventArgs e)
        {
            txtMaHV.Text = dgvHocVien.Rows[e.RowIndex].Cells[0].Value.ToString();
            txtHoTen.Text = dgvHocVien.Rows[e.RowIndex].Cells[1].Value.ToString();
            dtpNgaySinh.Value = Convert.ToDateTime(dgvHocVien.Rows[e.RowIndex].Cells[2].Value); ;
            if (Convert.ToBoolean(dgvHocVien.Rows[e.RowIndex].Cells[3].Value) == true)
            {
                radNam.Checked = true;
            }
            else
                radNu.Checked = true;
            txtDiem1.Text = dgvHocVien.Rows[e.RowIndex].Cells[4].Value.ToString();
            txtDiem2.Text = dgvHocVien.Rows[e.RowIndex].Cells[5].Value.ToString();


        }

        private void btnSua_Click(object sender, EventArgs e)
        {
            CHocVien hv = new CHocVien();
            hv.mahv = txtMaHV.Text;
            hv.hoTen = txtHoTen.Text;
            hv.ngaySinh = dtpNgaySinh.Value.Date;
            hv.gioiTinh = radNam.Checked;
            hv.diem1 = hv.diem1 = double.Parse(txtDiem1.Text);
            hv.diem2 = hv.diem2 = double.Parse(txtDiem2.Text);
            xuLyHV.suaHV(hv);
            hienThiDSHV();
        }

        private void btnXoa_Click(object sender, EventArgs e)
        {
            string mahv = txtMaHV.Text;
            xuLyHV.xoaHV(mahv);
            hienThiDSHV();
        }

        private void btnLuu_Click(object sender, EventArgs e)
        {
            bool kqGhiFile = CTruyCapDuLieu.ghiFile("hv.dat");
            if (kqGhiFile == true)
                MessageBox.Show("Ghi File thành công");
            else
                MessageBox.Show("Ghi File thất bại");
        }

        private void btnThoat_Click(object sender, EventArgs e)
        {
            Close();
        }

        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {
            if (MessageBox.Show("Bạn có muốn thoát không?", "Thông báo", MessageBoxButtons.YesNo, MessageBoxIcon.Error) == DialogResult.No) 
                e.Cancel = true;
        }
    }
}