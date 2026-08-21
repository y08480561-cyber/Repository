# Repository
{   "name": "postersaz-email-backend",   "version": "1.0.0",   "main": "server.js",   "scripts": {     "start": "node server.js"   },   "dependencies": {     "express": "^4.19.2",     "nodemailer": "^6.9.13"   } }
const express = require('express');
const nodemailer = require('nodemailer');
const app = express();

app.use(express.json());

const transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
        user: process.env.SMTP_USER,
        pass: process.env.SMTP_PASSWORD
    }
});

app.post('/api/v1/orders/notify-admin', async (req, res) => {
    try {
        const order = req.body;
        const adminEmail = process.env.ADMIN_EMAIL || 'y08480561@gmail.com';

        const mailOptions = {
            from: `"سامانه پوسترساز" <${process.env.SMTP_USER}>`,
            to: adminEmail,
            subject: order.subject || `سفارش جدید طراحی پوستر - ${order.orderId}`,
            text: order.plainBody,
            html: order.htmlBody
        };

        await transporter.sendMail(mailOptions);
        console.log(`سفارش شماره ${order.orderId} با موفقیت به ایمیل مدیر ارسال شد.`);
        return res.status(200).json({ success: true });
    } catch (error) {
        console.error('خطا در ارسال ایمیل:', error);
        return res.status(500).json({ success: false, error: error.message });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server is running on port ${PORT}`));
