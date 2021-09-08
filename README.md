# Banban
Receipt And Report printing using System Printing Drawstring

private void A4Print(DataSet _data, System.Drawing.Printing.PrintPageEventArgs e)

        {
            Graphics graphics = e.Graphics;

            int paperWidth = Convert.ToInt16(e.PageBounds.Width - 30);

            Font font12 = new Font("AngsanaUPC", 12);
            Font font14 = new Font("AngsanaUPC", 14);
            Font font16 = new Font("AngsanaUPC", 16, FontStyle.Bold);
            Font font18 = new Font("AngsanaUPC", 18, FontStyle.Bold);
            Font font20 = new Font("AngsanaUPC", 20, FontStyle.Bold);
            Font font22 = new Font("AngsanaUPC", 22, FontStyle.Bold);
            Font font24 = new Font("AngsanaUPC", 24, FontStyle.Bold);

            int leading = 4;
            int lineHeight12 = Convert.ToInt16(font12.GetHeight() + leading);
            int lineHeight14 = Convert.ToInt16(font14.GetHeight() + leading);
            int lineHeight16 = Convert.ToInt16(font16.GetHeight() + leading);
            int lineHeight18 = Convert.ToInt16(font18.GetHeight() + leading);
            int lineHeight20 = Convert.ToInt16(font20.GetHeight() + leading);
            int lineHeight22 = Convert.ToInt16(font22.GetHeight() + leading);
            int lineHeight24 = Convert.ToInt16(font24.GetHeight() + leading);

            int StartX = 0;
            int StartY = leading;
            int Offset = 0;

            int position_x = StartX;

            StringFormat left = new StringFormat(StringFormatFlags.NoClip);
            StringFormat center = new StringFormat(left);
            StringFormat right = new StringFormat(left);

            center.Alignment = StringAlignment.Center;
            left.Alignment = StringAlignment.Near;
            right.Alignment = StringAlignment.Far;

            Brush brush = Brushes.Black;
            Pen pen = new Pen(color: Color.Red, 1);

            int charFit = 0;
            int lineFill = 0;
            SizeF wrap = graphics.MeasureString(txtHeader.Text, font24, new SizeF(), center, out charFit, out lineFill);

            Size layoutSize = new Size(paperWidth, lineHeight14 * 3);
            Point layoutPoint = new Point(StartX, StartY + Offset);
            int column_size = paperWidth;

            //HEADER
            //block
            Rectangle layout = new Rectangle(layoutPoint, layoutSize);

            //logo
            layoutPoint = new Point(StartX, StartY + Offset);
            layoutSize = new Size(column_size / 10, layout.Height);
            layout = new Rectangle(layoutPoint, layoutSize);
            //graphics.DrawRectangle(new Pen(Color.Cyan,1), layout);
            try
            {
                Image logo = Image.FromFile($"{Application.StartupPath}\\logo_64.png");
                graphics.DrawImage(logo, layout);
            }
            catch { }

            //middle
            //BranchName
            column_size -= layout.Width;
            layoutPoint = new Point(layout.X + layout.Width, StartY + Offset);
            layoutSize = new Size(column_size - 150, layout.Height);
            layout = new Rectangle(layoutPoint, layoutSize);
            //graphics.DrawRectangle(new Pen(Color.Blue, 1), layout);
            graphics.DrawString($"{_data.Tables["dtSell"].Select().First()["BName"]}", font18, brush, layout, left);


            //address
            graphics.DrawString($"ที่อยู่ : {_data.Tables["dtSell"].Select().Single()["Address"]}", font14, brush, new Rectangle(new Point(layoutPoint.X, layoutPoint.Y + lineHeight18), new Size(layoutSize.Width - 100, layoutSize.Height / 2)), left);

            graphics.DrawString($"โทร : {_data.Tables["dtSell"].Select().Single()["Phone"]}", font14, brush, new Rectangle(new Point(layoutPoint.X, layoutPoint.Y + lineHeight14 * 2), new Size(layoutSize.Width - 100, layoutSize.Height / 2)), left);
            graphics.DrawString($"TAX : {_data.Tables["dtSell"].Select().Single()["Tax"]}", font14, brush, new Rectangle(new Point(layoutPoint.X + charFit * 15, layoutPoint.Y + lineHeight14 * 2), new Size(layoutSize.Width - 100, layoutSize.Height / 2)), left);

            //headtext
            column_size -= layout.Width;

            wrap = graphics.MeasureString(txtHeader.Text, font24, new Size(column_size, lineHeight24 * 2), center, out charFit, out lineFill);
            layoutPoint = new Point(layout.X + layout.Width, StartY + Offset);
            layoutSize = new Size(column_size, lineHeight24 * lineFill);
            layout = new Rectangle(layoutPoint, layoutSize);
            graphics.SmoothingMode = SmoothingMode.HighQuality;
            graphics.DrawPath(new Pen(Color.Black, 1), this.BorderRadius(layout, 20));
            //graphics.FillPath(Brushes.Aquamarine, this.BorderRadius(layout, 20));
            graphics.DrawString($"{txtHeader.Text.Replace("/", $"/{System.Environment.NewLine}")}", font24, brush, layout, center);

            //end header


            //start customer row
            Offset += lineHeight16 * 3;
            layoutPoint = new Point(StartX, StartY + Offset);
            layoutSize = new Size(paperWidth, lineHeight18 * 3);
            layout = new Rectangle(layoutPoint, layoutSize);
            //graphics.DrawRectangle(pen,layout);

            //customer detail column
            column_size = layout.Width;
            layoutPoint = new Point(layout.X, layout.Y);
            layoutSize = new Size(column_size - 300, layout.Height);
            layout = new Rectangle(layoutPoint, layoutSize);
            graphics.DrawPath(new Pen(Color.Black, 1), this.BorderRadius(layout, 20));
            //graphics.DrawRectangle(pen, layout);
            graphics.DrawString($"ชื่อลูกค้า :  {_data.Tables["dtCustomer"].Select().Single()["CName"]}", font14, brush, layout, left);

            //customer address
            wrap = graphics.MeasureString($"ที่อยู่ :  {_data.Tables["dtCustomer"].Select().Single()["Address"]}", font14, new Size(layout.Width, 14 * 2), center, out charFit, out lineFill);
            graphics.DrawString($"ที่อยู่ :  {_data.Tables["dtCustomer"].Select().Single()["Address"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Top + (lineHeight14)), new Size(layout.Width, lineHeight14 * lineFill)), left);

            graphics.DrawString($"โทร :  {_data.Tables["dtCustomer"].Select().Single()["Phone"]}    TAXID : {_data.Tables["dtCustomer"].Select().Single()["Phone"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Top + (lineHeight14 * lineFill + lineHeight14)), new Size(layout.Width, lineHeight18)), left);

            //doc column
            column_size -= layout.Width;
            layoutPoint = new Point(layout.Right + 50, layout.Y);
            layoutSize = new Size(column_size - 50, layout.Height);
            layout = new Rectangle(layoutPoint, layoutSize);
            graphics.DrawPath(new Pen(Color.Black, 1), this.BorderRadius(layout, 20));
            //graphics.DrawRectangle(pen, layout);
            graphics.DrawString($"เลขที่บิล :  {_data.Tables["dtSell"].Select().Single()["DocNo"]}", font14, brush, new Rectangle(new Point(layout.X + 10, layout.Top), new Size(layout.Width, lineHeight18)), left);
            graphics.DrawString($"วันที่ขาย :  {_data.Tables["dtSell"].Select().Single()["SellDate"]}", font14, brush, new Rectangle(new Point(layout.X + 10, layout.Top + (lineHeight20)), new Size(layout.Width, lineHeight18)), left);
            graphics.DrawString($"พนักงาน :  {_data.Tables["dtSell"].Select().Single()["OfficerName"]}", font14, brush, new Rectangle(new Point(layout.X + 10, layout.Top + (lineHeight20 * 2)), new Size(layout.Width, lineHeight18)), left);


            //list product column
            Offset += lineHeight20 * 3;
            layoutPoint = new Point(StartX, StartY + Offset);
            layoutSize = new Size(paperWidth, lineHeight14);
            layout = new Rectangle(layoutPoint, layoutSize);
            graphics.DrawRectangle(new Pen(Color.Black, 1), layout);

            //table column
            Rectangle col_number = new Rectangle(layoutPoint, new Size(70, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.Green), col_number);
            graphics.DrawString($"ลำดับ", font16, brush, col_number, center);

            Rectangle col_product_name = new Rectangle(new Point(col_number.Right, col_number.Top), new Size(290, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.Blue), col_product_name);
            graphics.DrawString($"ชื่อสินค้า", font16, brush, col_product_name, center);

            Rectangle col_qty = new Rectangle(new Point(col_product_name.Right, col_product_name.Top), new Size(100, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.Yellow), col_qty);
            graphics.DrawString($"จำนวน", font16, brush, col_qty, center);

            Rectangle col_unit = new Rectangle(new Point(col_qty.Right, col_qty.Top), new Size(70, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.Aqua), col_unit);
            graphics.DrawString($"หน่วย", font16, brush, col_unit, center);

            Rectangle col_price = new Rectangle(new Point(col_unit.Right, col_unit.Top), new Size(100, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.HotPink), col_price);
            graphics.DrawString($"ราคา/หน่วย", font16, brush, col_price, center);

            Rectangle col_sum = new Rectangle(new Point(col_price.Right, col_price.Top), new Size(167, layout.Height));
            //graphics.DrawRectangle(new Pen(Color.Chocolate), col_sum);
            graphics.DrawString($"รวมราคา", font16, brush, col_sum, center);

            Offset += lineHeight16;
            //table detail
            while (datarow < _data.Tables["dtSellD"].Rows.Count)
            {
                DataRow row = _data.Tables["dtSellD"].Rows[datarow];

                wrap = graphics.MeasureString(row["ProductName"].ToString(), font14, new SizeF(col_product_name.Width, lineHeight14 * 2), left, out charFit, out lineFill);

                layoutPoint = new Point(StartX, Offset);
                layoutSize = new Size(paperWidth, lineHeight14 * lineFill);
                layout = new Rectangle(layoutPoint, layoutSize);
                //graphics.DrawRectangle(new Pen(Color.Black,1), layout);


                col_number = new Rectangle(layoutPoint, new Size(70, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.Green), col_number);
                graphics.DrawString($"{this.datarow + 1}", font14, brush, col_number, center);

                col_product_name = new Rectangle(new Point(col_number.Right, col_number.Top), new Size(290, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.Blue), col_product_name);
                graphics.DrawString($"{row["ProductName"]}", font14, brush, col_product_name, left);

                col_qty = new Rectangle(new Point(col_product_name.Right, col_product_name.Top), new Size(100, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.Yellow), col_qty);
                graphics.DrawString($"{row["Number"]}", font14, brush, col_qty, center);

                col_unit = new Rectangle(new Point(col_qty.Right, col_qty.Top), new Size(70, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.Aqua), col_unit);
                graphics.DrawString($"{row["Unit"]}", font14, brush, col_unit, center);

                col_price = new Rectangle(new Point(col_unit.Right, col_unit.Top), new Size(100, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.HotPink), col_price);
                graphics.DrawString($"{row["UnitPrice"]}", font14, brush, col_price, center);

                col_sum = new Rectangle(new Point(col_price.Right, col_price.Top), new Size(167, layout.Height));
                //graphics.DrawRectangle(new Pen(Color.Chocolate), col_sum);
                graphics.DrawString($"{row["SumPrice"]}", font14, brush, col_sum, center);

                datarow++;

                Offset += lineHeight14 * lineFill;

                if ((layout.Bottom + 350) > e.PageBounds.Height && datarow != _data.Tables["dtSelld"].Rows.Count)
                {
                    Offset = StartY + leading;
                    e.HasMorePages = true;
                    this.page--;
                    return;
                }
            }


            layoutPoint = new Point(StartX, StartY + e.PageBounds.Height - 300);
            layoutSize = new Size(paperWidth, lineHeight16);
            layout = new Rectangle(layoutPoint, layoutSize);
            //graphics.DrawRectangle(new Pen(Color.Black, 1), layout);

            //สรุปยอดเงิน
            string thaibaht = this.ThaiBahtText(_data.Tables["dtSell"].Select().Single()["GrandTotal"].ToString());
            layout = new Rectangle(new Point(layout.X, layout.Y), new Size(paperWidth - 300, layout.Height));
            graphics.DrawRectangle(new Pen(brush, 1), layout);
            graphics.DrawString($"{thaibaht}", font16, brush, layout, center);
            graphics.DrawString($"ส่วนลดจากการใช้แต้ม : {_data.Tables["dtSell"].Select().Single()["DiscountPoint"]}", font16, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight16), new Size(layout.Width, lineHeight16)), left);

            if (SellCredit.Tables["dtSellPoint"].Rows.Count > 0)
            {
                graphics.DrawString($"Get Point : " +
                    $"{_data.Tables["dtSellPoint"].Select().Single()["PointIN"]}   " +
                    $"Used Point :  {_data.Tables["dtSellPoint"].Select().Single()["PointOut"]}" +
                    $"Point Balance :   {_data.Tables["dtSellPoint"].Select().Single()["PointBalance"]}",
                    font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 2),
                    new Size(layout.Width, lineHeight14)), left);
            }

            if (_data.Tables["dtSellPayment"].Rows.Count > 0)
            {
                double cash = 0;
                double transfer = 0;
                double credit = 0;

                foreach (DataRow row in _data.Tables["dtSellPayment"].Rows)
                {
                    if (row["Type"].ToString() == "Cash") cash += Convert.ToDouble(row["Amount"]);
                    if (row["Type"].ToString() == "Transfer") transfer += Convert.ToDouble(row["Amount"]);
                    if (row["Type"].ToString() == "Credit") credit += Convert.ToDouble(row["Amount"]);
                }


                graphics.DrawString($"**    ชำระโดย เงินสด   {cash}      เงินโอน {transfer}      เงินจ่ายล่วงหน้า {credit}  **", font16, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 3), new Size(layout.Width, lineHeight14)), left);
            }

            graphics.DrawString($"{txtLastText.Text}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 4), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"{txtLastText2.Text}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 5), new Size(layout.Width, lineHeight14)), left);


            //รายละเอียดของยอดเงิน
            layout = new Rectangle(new Point(layout.Right + 50, layout.Y), new Size(paperWidth - layout.Width - 50, lineHeight14 * 6));
            graphics.DrawRectangle(new Pen(brush, 1), layout);
            graphics.DrawString($"จำนวนเงิน :    {_data.Tables["dtSell"].Select().Single()["Total"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Y), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"ส่วนลด :    {_data.Tables["dtSell"].Select().Single()["Discount"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"Service Charge :    {_data.Tables["dtSell"].Select().Single()["ServiceCharge"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 2), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"%Vat :    {_data.Tables["dtSell"].Select().Single()["PVat"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 3), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"Vat :    {_data.Tables["dtSell"].Select().Single()["Vat"]}", font14, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 4), new Size(layout.Width, lineHeight14)), left);
            graphics.DrawString($"รวมทั้งหมด :    {_data.Tables["dtSell"].Select().Single()["GrandTotal"]}", font16, brush, new Rectangle(new Point(layout.X, layout.Y + lineHeight14 * 5), new Size(layout.Width, lineHeight16)), left);


            //ลายเซ็นต์ท้ายบิล
            layoutPoint = new Point(StartX, StartY + e.PageBounds.Height - 120);
            layoutSize = new Size(paperWidth, lineHeight14*3);
            layout = new Rectangle(layoutPoint, layoutSize);
            //graphics.DrawRectangle(pen,layout);


            //ผู้รับเงิน
            graphics.DrawString($"ลายมือชื่อ",font16,brush, new Rectangle(new Point(layout.Left, layout.Top), new Size((paperWidth - 200) / 2, lineHeight16)), center);
            graphics.DrawString($"ลงชื่อ....................................................{txtMoneyRecieve.Text}",font14,brush, new Rectangle(new Point(layout.Left, layout.Top + (lineHeight16)), new Size((paperWidth - 200) / 2, lineHeight16)), center);
            graphics.DrawString($"(............................................)", font14, brush, new Rectangle(new Point(layout.Left, layout.Top + (lineHeight14*2)), new Size((paperWidth - 200) / 2, lineHeight16)), center);

            //ผู้รับสินค้า
            graphics.DrawString($"ลายมือชื่อ", font16, brush, new Rectangle(new Point(layout.Left + ((paperWidth - 120) / 2), layout.Top), new Size((paperWidth - 200) / 2, lineHeight16)), center);
            graphics.DrawString($"ลงชื่อ....................................................{txtReciever.Text}", font14, brush, new Rectangle(new Point(layout.Left + ((paperWidth - 120) / 2), layout.Top + (lineHeight16)), new Size((paperWidth - 200) / 2, lineHeight16)), center);
            graphics.DrawString($"(............................................)", font14, brush, new Rectangle(new Point(layout.Left+((paperWidth - 120) / 2), layout.Top + (lineHeight14 * 2)), new Size((paperWidth - 200) / 2, lineHeight16)), center);

        }
