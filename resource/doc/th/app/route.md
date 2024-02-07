## ไฟล์กำหนดเส้นทาง
ไฟล์กำหนดเส้นทางของปลั๊กอินอยู่ที่ `plugin/ชื่อปลั๊กอิน/config/route.php`

## เส้นทางเริ่มต้น
ที่อยู่ URL ของแอปพลิเคชันทั้งหมดจะเริ่มต้นด้วย `/app` เช่น `plugin\foo\app\controller\UserController` จะมีที่อยู่ URL เป็น `http://127.0.0.1:8787/app/foo/user`

## ปิดเส้นทางเริ่มต้น
หากต้องการปิดเส้นทางเริ่มต้นของแอปพลิเคชันปลั๊กอินบางตัว สามารถตั้งค่าในไฟล์กำหนดเส้นทางได้ดังนี้
```php
Route::disableDefaultRoute('foo');
```

## จัดการการตอบกลับ 404
หากต้องการกำหนด fallback สำหรับแอปพลิเคชันปลั๊กอินบางตัว สามารถทำได้โดยการส่งชื่อปลั๊กอินผ่านพารามิเตอร์ที่สอง ตัวอย่างเช่น
```php
Route::fallback(function(){
    return redirect('/');
}, 'foo');
```