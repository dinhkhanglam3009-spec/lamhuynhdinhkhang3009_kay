using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace lap5._3
{
    public partial class Form1 : Form
    {
        private string currentFile = "";
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            // 2.1 Load font vào combobox
            foreach (FontFamily font in new InstalledFontCollection().Families)
            {
                cmbFonts.Items.Add(font.Name);
            }
            cmbFonts.SelectedItem = "Tahoma"; // mặc định Tahoma

            // Load size vào combobox
            int[] sizes = { 8, 9, 10, 11, 12, 14, 16, 18, 20, 22, 24, 26, 28, 36, 48, 72 };
            foreach (int s in sizes)
            {
                cmbSize.Items.Add(s);
            }
            cmbSize.SelectedItem = 14; // mặc định size 14

            // Set mặc định cho RichTextBox
            richText.Font = new Font("Tahoma", 14);
        }
        private void btnNew_Click(object sender, EventArgs e)
        {
            richText.Clear();
            richText.Font = new Font("Tahoma", 14);
            cmbFonts.SelectedItem = "Tahoma";
            cmbSize.SelectedItem = 14;
            currentFile = "";
        }

        // 2.3 Mở tập tin
        private void btnOpen_Click(object sender, EventArgs e)
        {
            OpenFileDialog dlg = new OpenFileDialog();
            dlg.Filter = "Text Files|*.txt|Rich Text Format|*.rtf";

            if (dlg.ShowDialog() == DialogResult.OK)
            {
                currentFile = dlg.FileName;
                if (Path.GetExtension(currentFile).ToLower() == ".rtf")
                {
                    richText.LoadFile(currentFile, RichTextBoxStreamType.RichText);
                }
                else
                {
                    richText.LoadFile(currentFile, RichTextBoxStreamType.PlainText);
                }
            }
        }

        // 2.4 Lưu tập tin
        private void btnSave_Click(object sender, EventArgs e)
        {
            if (string.IsNullOrEmpty(currentFile))
            {
                SaveFileDialog dlg = new SaveFileDialog();
                dlg.Filter = "Rich Text Format|*.rtf";

                if (dlg.ShowDialog() == DialogResult.OK)
                {
                    currentFile = dlg.FileName;
                    richText.SaveFile(currentFile, RichTextBoxStreamType.RichText);
                }
            }
            else
            {
                richText.SaveFile(currentFile, RichTextBoxStreamType.RichText);
                MessageBox.Show("Lưu thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
        }

        // 2.5 In đậm
        private void btnBold_Click(object sender, EventArgs e)
        {
            ChangeFontStyle(FontStyle.Bold);
        }

        // In nghiêng
        private void btnItalic_Click(object sender, EventArgs e)
        {
            ChangeFontStyle(FontStyle.Italic);
        }

        // Hàm đổi kiểu font
        private void ChangeFontStyle(FontStyle style)
        {
            if (richText.SelectionFont != null)
            {
                Font currentFont = richText.SelectionFont;
                FontStyle newStyle;

                // Toggle trạng thái (bật/tắt)
                if (currentFont.Style.HasFlag(style))
                    newStyle = currentFont.Style & ~style;
                else
                    newStyle = currentFont.Style | style;

                richText.SelectionFont = new Font(currentFont, newStyle);
            }
        }

        // Khi chọn font trong combobox
        private void cmbFonts_SelectedIndexChanged(object sender, EventArgs e)
        {
            if (richText.SelectionFont != null)
            {
                float currentSize = richText.SelectionFont.Size;
                richText.SelectionFont = new Font(cmbFonts.SelectedItem.ToString(), currentSize);
            }
        }

        // Khi chọn size trong combobox
        private void cmbSize_SelectedIndexChanged(object sender, EventArgs e)
        {
            if (richText.SelectionFont != null)
            {
                string fontName = richText.SelectionFont.FontFamily.Name;
                float newSize = float.Parse(cmbSize.SelectedItem.ToString());
                richText.SelectionFont = new Font(fontName, newSize);
            }
        }
    }
}
