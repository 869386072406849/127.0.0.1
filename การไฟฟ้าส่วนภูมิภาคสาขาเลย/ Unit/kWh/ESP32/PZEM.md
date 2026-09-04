ไม่มีโค้ดคำสั่งหรือโปรแกรมสำเร็จรูปอย่างเป็นทางการจาก "การไฟฟ้า" (กฟภ. หรือ กฟน.) ที่เปิดให้ดาวน์โหลดบน GitHub สำหรับคำนวณค่าไฟฟ้าจากมิเตอร์ต่อวันโดยตรงแต่คุณสามารถหาโค้ดตัวอย่างสำหรับคำนวณค่าไฟ หรือโปรแกรมอ่านค่าพลังงานจากสมาร์ตมิเตอร์ (Smart Meter) ที่นักพัฒนาไทยและต่างชาติทำไว้บน GitHub ได้ เช่น โค้ดอ่านค่าพลังงานไฟฟ้าผ่านไมโครคอนโทรลเลอร์หรือตัวอย่างการคำนวณค่าไฟเบื้องต้นใน Electricity Billing System [1] (https://github.com/topics/electricity-consumption?l=c&o=asc&s=forks), [2] (https://github.com/topics/electricity-meter?l=python&o=desc&s=stars), [3] (https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/)แนวทางการสร้างโค้ดคำนวณค่าไฟต่อวันหากต้องการเขียนโปรแกรมเอง สามารถใช้หลักการคำนวณคร่าวๆ ดังนี้:อ่านหน่วย (Unit / kWh) ที่ใช้ไปในแต่ละวันเทียบอัตราค่าไฟฟ้า ตามประเภทบ้านพักอาศัยหรือกิจการของการไฟฟ้าคำนวณค่าพลังงานไฟฟ้า + ค่าบริการรายเดือนเฉลี่ยต่อวัน + Ft + VATหากคุณต้องการให้ช่วยเขียนโค้ดตัวอย่าง (เช่น ภาษา Python หรือ JavaScript) สำหรับ คำนวณค่าไฟต่อวันจากหน่วยที่ใช้ หรือ วิธีเชื่อมต่อกับอุปกรณ์วัดไฟ (ESP32/PZEM) สามารถบอกรายละเอียดเพิ่มเติมได้เลยครับ
<!-- wp:paragraph -->
<p><a class="navbar-brand" href="https://iot-kmutnb.github.io/blogs/">IoT Engineering Education</a></p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":1} -->
<h1 class="wp-block-heading" id="zm194-d9y">การอ่านค่าจากเพาเวอร์มิเตอร์ไฟฟ้าสามเฟส: ZM194-D9Y</h1>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>บทความนี้กล่าวถึง ตัวอย่างการเขียนโปรแกรม&nbsp;<strong>Arduino Sketch</strong>&nbsp;และใช้งานบอร์ดไมโครคอนโทรลเลอร์&nbsp;<strong>Arduino ESP32</strong>&nbsp;และภาษา&nbsp;<strong>Python</strong>&nbsp;เพื่ออ่านค่าจากมิเตอร์วัดกำลังไฟฟ้าแบบ 3 เฟส รุ่น&nbsp;<strong>ZM194-D9Y</strong>&nbsp;โดยใช้โพรโทคอล&nbsp;<strong>RS485 / Modbus RTU</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>Keywords:</strong>&nbsp;<em>3-Phase Power Meter</em>,&nbsp;<em>Arduino Sketch</em>,&nbsp;<em>ESP32-C3</em>,&nbsp;<em>RS485</em>,&nbsp;<em>Modbus RTU</em>,&nbsp;<em>Python</em></p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="_1">▷&nbsp;<strong>เพาเวอร์มิเตอร์แบบดิจิทัล</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>มิเตอร์วัดกำลังไฟฟ้า หรือ "เพาเวอร์มิเตอร์" แบบดิจิทัล (<strong>Digital Power Meter</strong>) เป็นอุปกรณ์ที่ใช้สำหรับการวัดค่าหรือพารามิเตอร์ต่าง ๆ สำหรับไฟฟ้ากระแสสลับ เช่น</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>ความถี่ (<strong>Hz</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>แรงดันไฟฟ้า (<strong>V</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>กระแสไฟฟ้า (<strong>I</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ค่าเพาเวอร์แฟคเตอร์ หรือ ค่าตัวประกอบกำลังไฟฟ้า (<strong>Power Factor</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>กำลังไฟฟ้าที่ใช้งานจริง (<strong>Real Power: kW</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>กำลังไฟฟ้าที่ปรากฏ (<strong>Apparent Power: kVA</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>กำลังไฟฟ้ารีแอคทีฟ (<strong>Reactive Power: kVAR</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>พลังงานไฟฟ้าที่ใช้ (<strong>Energy: kWh</strong>)</li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>มิเตอร์ไฟฟ้าประเภทนี้ จำแนกได้ตามจำนวนเฟสไฟฟ้าที่ต้องการวัดทางไฟฟ้า ได้แก่ มิเตอร์แบบเฟสเดียว (<strong>Single-Phase Power Meter</strong>) และมิเตอร์แบบสามเฟส (<strong>3-Phase Power Meter</strong>) นอกจากมีหน้าจอแสดงผลแบบ&nbsp;<strong>LCD</strong>&nbsp;มิเตอร์ไฟฟ้าแบบดิจิทัลในยุคปัจจุบัน ยังรองรับการเชื่อมต่อด้วยโพรโทคอล&nbsp;<strong>Modbus RTU</strong>&nbsp;และเชื่อมต่อกับระบบบัส&nbsp;<strong>RS485</strong>&nbsp;ได้ด้วย</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>การต่อวงจรไฟฟ้าเพื่อใช้งานมิเตอร์ มีหลายรูปแบบ โดยดูจากความจำเป็นที่ต้องใช้อุปกรณ์อื่นร่วมด้วยหรือไม่ เช่น การใช้หม้อแปลงแรงดันไฟฟ้า (<strong>Potential Transformer: PT</strong>) และหม้อแปลงกระแสไฟฟ้า (<strong>Current Transformer: CT</strong>) เพื่อการลดทอนแรงดันไฟฟ้าด้วย&nbsp;<strong>PT</strong>&nbsp;หรือลดกระแสไฟฟ้าด้วย&nbsp;<strong>CT</strong>&nbsp;ให้มีค่าต่ำลงและอยู่ในช่วงเหมาะสมกับมิเตอร์ไฟฟ้า</p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="zm194-d9y_1">▷&nbsp;<strong>มิเตอร์สามเฟส: ZM194-D9Y</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>เพาเวอร์มิเตอร์ที่ได้นำมาทดลองใช้งานเพื่อการสาธิตการเขียนโปรแกรมและการเชื่อมต่อสื่อสารข้อมูล คือ โมเดล&nbsp;<strong>ZM194-D9Y</strong>&nbsp;(แบรนด์สินค้า:&nbsp;<strong>Zhang Ming (ZJZM)</strong>)</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/zm194-d9y.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: เพาเวอร์มิเตอร์&nbsp;<strong>ZiZm ZM194-D9Y</strong>&nbsp;(มุมมองด้านหน้า) และช่องเสียบสายไฟที่อยู่ด้านหลังของมิเตอร์ไฟฟ้า</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>เป็นเพาเวอร์มิเตอร์แบบสามเฟส ผลิตในประเทศจีน</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>สามารถเชื่อมต่อกับ <strong>RS485</strong> และสื่อสารข้อมูลด้วยโพรโทคอล <strong>Modbus RTU</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>รองรับการตั้งค่า <strong>Baudrate</strong> มี 3 ตัวเลือก ได้แก่ <strong>1200</strong> , <strong>4800</strong> และ <strong>9600</strong> (สูงสุด)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>รองรับการวัดแรงดันไฟฟ้าในช่วง <strong>380V (AC)</strong> ต่อเฟส และกระแสไฟฟ้าไม่เกิน <strong>5A</strong> ต่อเฟส</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>คลาสของความแม่นยำ (<strong>Accuracy Class</strong>): <strong>0.5</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ใช้แรงดันไฟเลี้ยง (<strong>Power Supply</strong>) ได้ในช่วง <strong>AC/DC 85V ~ 265V</strong> โดยต่อเข้าที่ช่อง (หมายเลข) ต่อไปนี้<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li><strong>L</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>N</strong></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list --></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ช่องสำหรับวัดกระแสไฟฟ้าแต่ละเฟส (<strong>Current Signal Inputs: Iin</strong>)<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li><strong>Ia⭑</strong> (4) และ <strong>Ia</strong> (5) — กระแสไหลเข้าและออกสำหรับเฟส <strong>a</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>Ib⭑</strong> (6) และ <strong>Ib</strong> (7) — กระแสไหลเข้าและออกสำหรับเฟส <strong>b</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>Ic⭑</strong> (8) และ <strong>Ia</strong> (9) — กระแสไหลเข้าและออกสำหรับเฟส <strong>c</strong></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list --></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ช่องสำหรับวัดแรงดันไฟฟ้าแต่ละเฟส (<strong>Voltage Signal Inputs: Vin</strong>)<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li><strong>L1</strong> (11) และ <strong>N</strong> (14) — แรงดันไฟฟ้าเฟส <strong>a</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>L2</strong> (12) และ <strong>N</strong> (14) — แรงดันไฟฟ้าเฟส <strong>b</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>L3</strong> (13) และ <strong>N</strong> (14) — แรงดันไฟฟ้าเฟส <strong>c</strong></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list --></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ช่องสัญญาณสำหรับ <strong>RS485</strong> (Differential Signaling)<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li><strong>A</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><strong>B</strong></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list --></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>การต่อวงจรเพื่อใช้งานมิเตอร์ ได้เลือกรูปแบบ&nbsp;<strong>3-phase, 4-wire</strong>&nbsp;(ไม่มีการต่อหม้อแปลงภายนอก&nbsp;<strong>PT</strong>&nbsp;และ&nbsp;<strong>CT</strong>)</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/zm194-d9y-2.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: กล่องอุปกรณ์เพาเวอร์มิเตอร์สำหรับการทดลอง</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>จากรูปอุปกรณ์ จะเห็นว่า ทางซ้ายมือของ&nbsp;<strong>Front Panel</strong>&nbsp;มีช่องเสียบแบบ&nbsp;<strong>Banana Jack Sockets 4mm</strong>&nbsp;สำหรับไฟเข้า 3 เฟส (<em>N</em>,&nbsp;<em>L1</em>,&nbsp;<em>L2</em>,&nbsp;<em>L3</em>) และทางขวามือมีช่องเสียบ สำหรับไฟเข้า 3 เฟส (เรียงตามลำดับ) เพื่อนำไปใช้กับโหลดไฟฟ้า</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>เนื่องจากไม่มีระบบไฟฟ้าสามเฟสให้ทดลอง จึงใช้เพียงหนึ่งเฟสเท่านั้น โดยต่อเข้าที่&nbsp;<strong>L1</strong>&nbsp;และ&nbsp;<strong>N</strong>&nbsp;และนำไปต่อกับโหลดไฟฟ้า เช่น หลอดไฟ หรือ กาต้มน้ำร้อนไฟฟ้า</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/power_meter_setup.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: อุปกรณ์สำหรับการทดลอง</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>ข้อสังเกต:</strong>&nbsp;อุปกรณ์&nbsp;<strong>ZM194-D9Y</strong>&nbsp;ที่ได้นำมาทดลอง ภายในมีการต่อสายไฟและใช้แรงดันไฟเลี้ยงจาก&nbsp;<strong>L1</strong>&nbsp;และ&nbsp;<strong>N</strong></p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="rs-485">▷&nbsp;<strong>อุปกรณ์สำหรับการเชื่อมต่อด้วยบัส RS-485</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>ในการเชื่อมต่อกับมิเตอร์ไฟฟ้าผ่านบัส&nbsp;<strong>RS485</strong>&nbsp;ได้เลือก 2 รูปแบบสำหรับการสาธิต</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>การเชื่อมต่อด้วยคอมพิวเตอร์ผ่านทางพอร์ต <strong>USB</strong> โดยใช้โมดูล <strong>USB-to-RS485</strong> (ภายในใช้ชิป <strong>USB-Serial</strong> อย่างเช่น <strong>CH340</strong> หรือ <strong>PL2303</strong>) และเขียนโปรแกรมด้วย <strong>Python</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>การเชื่อมต่อด้วยไมโครคอนโทรลเลอร์ (ใช้บอร์ด <strong>ESP32-C3 Super-Mini</strong>)</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>โมดูล <strong>Serial-to-RS485</strong> และเขียนโปรแกรมด้วย <strong>Arduino Sketch</strong></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/usb_rs485_ch340.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างโมดูล&nbsp;<strong>USB-to-RS485</strong></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/rs485_modules.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างอุปกรณ์สำหรับการเชื่อมต่อกับบัส&nbsp;<strong>RS485</strong>&nbsp;สองประเภท</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>แนะนำให้ศึกษาเพิ่มเติมจากบทความที่เกี่ยวข้อง:&nbsp;<a href="https://iot-kmutnb.github.io/blogs/electronics/rs485_modules/">การใช้งานโมดูลสื่อสาร&nbsp;<strong>RS485 Transceiver</strong></a></p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="python">▷&nbsp;<strong>โค้ดตัวอย่าง: Python</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>ในการเขียนโค้ดด้วยภาษา&nbsp;<strong>Python</strong>&nbsp;เพื่อเชื่อมต่อกับอุปกรณ์&nbsp;<strong>RS485 - Modbus RTU</strong>&nbsp;สามารถทำได้สะดวกเนื่องจากมีไลบรารี เช่น&nbsp;<a href="https://github.com/pyhys/minimalmodbus"><code>minimalmodbus</code></a>&nbsp;และการใช้งานรวมถึงคำสั่งต่าง ๆ&nbsp;<strong>API</strong>&nbsp;ที่เกี่ยวข้อง ก็สามารถศึกษาในรายละเอียดได้จาก&nbsp;<a href="https://minimalmodbus.readthedocs.io/en/stable/"><strong>MinimalModbus Online Documentation</strong></a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ในการเขียนโค้ด&nbsp;<strong>Python</strong>&nbsp;แนะนำให้ใช้&nbsp;<strong>VS Code IDE</strong>:</p>
<!-- /wp:paragraph -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>ติดตั้ง <strong>VS Code IDE</strong> + ติดตั้ง <strong>Python Extensions</strong></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>สร้างและเปิด <strong>Folder</strong> ใหม่สำหรับการเขียนโค้ดทดลอง</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>สร้างไฟล์ เช่น <code>main.py</code> สำหรับการเขียนโค้ด <strong>Python</strong> และทดลองใช้โค้ดตัวอย่าง</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>สร้าง <strong>Python Virtual Environment</strong> สำหรับการติดตั้งแพ็คเกจทีต้องการใช้เพิ่ม</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>ติดตั้ง <code>minimalmodbus</code> โดยใช้คำสั่ง <code>python3 -m pip install minimalmodbus</code> หรือ <code>pip3 install minimalmodbus</code> (ทดลองใช้เวอร์ชัน <strong>v2.1.1</strong>) ใน <strong>Terminal</strong> ของ <strong>VS Code IDE</strong></li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-1.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-2.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-3.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-4.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างขั้นตอนการสร้าง&nbsp;<strong>Python Virtual Environment</strong>&nbsp;สำหรับ&nbsp;<strong>VS Code IDE</strong>&nbsp;และใช้งานภายใต้&nbsp;<strong>Folder</strong>&nbsp;ที่สร้างโดยผู้ใช้</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ดังนั้นโค้ดตัวอย่างต่อไปนี้สาธิตการใช้คำสั่งของ&nbsp;<code>minimalmodbus</code>&nbsp;โดยได้กำหนดหมายเลขอุปกรณ์ (<strong>Device Address</strong>) เป็น&nbsp;<code>1</code>&nbsp;และตั้งค่า&nbsp;<strong>Baudrate</strong>&nbsp;เป็น&nbsp;<code>9600</code>&nbsp;และเลือกใช้พอร์ต เช่น&nbsp;<code>/dev/ttyUSB0</code>&nbsp;(สำหรับ&nbsp;<strong>Linux</strong>)</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>การทำคำสั่งสำหรับ&nbsp;<strong>Modbus RTU</strong>&nbsp;จะใช้&nbsp;<strong>Function Code</strong>&nbsp;เป็น&nbsp;<code>0x03</code>&nbsp;เพื่อการอ่านค่ารีจิสเตอร์ (แต่ละตัวมีขนาด 16 บิต) ตามแอดเดรสของรีจิสเตอร์ในตารางข้างล่าง ค่าของพารามิเตอร์ทางไฟฟ้าแต่ละตัวมีขนาด 32 บิต (<strong>long integer</strong>) ดังนั้นจึงอ่านค่าจากรีจิสเตอร์ 2 ตัว เพื่อให้ได้ 32 บิต และเมื่ออ่านมาแล้ว จะต้องนำมาหารด้วย&nbsp;<code>1000.0</code>&nbsp;จึงจะได้ค่าเป็นเลขทศนิยมที่นำไปใช้ในการแสดงผล</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ข้อสังเกต: ค่ารีจิสเตอรีสำหรับแอดเดรสของอุปกรณ์ (<strong>Device Address</strong>:&nbsp;<code>1</code>&nbsp;= default) และการตั้งค่า&nbsp;<strong>Baudrate</strong>&nbsp;มีขนาด 16 บิต (<strong>short integer</strong>)SymbolRegister AddressDescriptionUA_REG_ADDR0x0000Phase-A Voltage [V]UB_REG_ADDR0x0002Phase-B Voltage [V]UC_REG_ADDR0x0004Phase-C Voltage [V]UAB_REG_ADDR0x0006Line-to-Line AB Voltage [V]UBC_REG_ADDR0x0008Line-to-Line BC Voltage [V]UCA_REG_ADDR0x000ALine-to-Line CA Voltage [V]IA_REG_ADDR0x000CPhase-A current [A]IB_REG_ADDR0x000EPhase-B current [A]IC_REG_ADDR0x0010Phase-C current [A]PFA_REG_ADDR0x0022Phase-A power factorPFB_REG_ADDR0x0024Phase-B power factorPFC_REG_ADDR0x0026Phase-C power factorTOTAL_P_REG_ADDR0x0018Total active power in kWTOTAL_Q_REG_ADDR0x0020Total reactive power in kVARTOTAL_S_REG_ADDR0x0030Total apparent power in kVAFREQ_REG_ADDR0x0032Frequency in HzBAUDRATE_REG_ADDR0x004ABaudrate (16-bit)DEV_ADDR_REG_ADDR0x0049Device Address (16-bit)<code class="language-python hljs">import time import minimalmodbus # slave address DEV_ADDR = 1 # serial port PORT_NAME = '/dev/ttyUSB0' FUNC_CODE = 0x03 UA_REG_ADDR = 0x0000 # Phase-A Voltage [V] UB_REG_ADDR = 0x0002 # Phase-B Voltage [V] UC_REG_ADDR = 0x0004 # Phase-C Voltage [V] UAB_REG_ADDR = 0x0006 # Line-to-Line AB Voltage [V] UBC_REG_ADDR = 0x0008 # Line-to-Line BC Voltage [V] UCA_REG_ADDR = 0x000A # Line-to-Line CA Voltage [V] IA_REG_ADDR = 0x000C # Phase-A current [A] IB_REG_ADDR = 0x000E # Phase-B current [A] IC_REG_ADDR = 0x0010 # Phase-C current [A] PFA_REG_ADDR = 0x0022 # Phase-A power factor PFB_REG_ADDR = 0x0024 # Phase-B power factor PFC_REG_ADDR = 0x0026 # Phase-C power factor TOTAL_P_REG_ADDR = 0x0018 # Total active power in kW TOTAL_Q_REG_ADDR = 0x0020 # Total reactive power in kVAR TOTAL_S_REG_ADDR = 0x0030 # Total apparent power in kVA FREQ_REG_ADDR = 0x0032 # Frequency in Hz BAUDRATE_REG_ADDR = 0x004A # Baudrate setting DEV_ADDR_REG_ADDR = 0x0049 # Device address params = [ {"reg_addr": UA_REG_ADDR, "name": "Ua", "unit": "V"}, {"reg_addr": UB_REG_ADDR, "name": "Ub", "unit": "V"}, {"reg_addr": UC_REG_ADDR, "name": "Uc", "unit": "V"}, {"reg_addr": UAB_REG_ADDR, "name": "Uab", "unit": "V"}, {"reg_addr": UBC_REG_ADDR, "name": "Ubc", "unit": "V"}, {"reg_addr": UCA_REG_ADDR, "name": "Uca", "unit": "V"}, {"reg_addr": IA_REG_ADDR, "name": "Ia", "unit": "A"}, {"reg_addr": IB_REG_ADDR, "name": "Ib", "unit": "B"}, {"reg_addr": IC_REG_ADDR, "name": "Ic", "unit": "C"}, {"reg_addr": PFA_REG_ADDR, "name": "PFa", "unit": "-"}, {"reg_addr": PFB_REG_ADDR, "name": "PFb", "unit": "-"}, {"reg_addr": PFC_REG_ADDR, "name": "PFc", "unit": "-"}, {"reg_addr": TOTAL_P_REG_ADDR, "name": "P", "unit": "kW"}, {"reg_addr": TOTAL_Q_REG_ADDR, "name": "Q", "unit": "kVAR"}, {"reg_addr": TOTAL_S_REG_ADDR, "name": "S", "unit": "kVA"}, {"reg_addr": FREQ_REG_ADDR, "name": "Freq", "unit": "Hz"}, ] # MODBUS device initialization device = minimalmodbus.Instrument(PORT_NAME, DEV_ADDR, debug=False) # MODBUS device connection settings device.serial.baudrate = 9600 device.serial.bytesize = 8 device.serial.parity = minimalmodbus.serial.PARITY_NONE device.mode = minimalmodbus.MODE_RTU device.serial.stopbits = 1 device.serial.timeout = 0.1 print("\n\n\n3-Phase Power Meter - RS485 Modbus-RTU Demo.... \n") value = device.read_register(registeraddress=DEV_ADDR_REG_ADDR, functioncode=FUNC_CODE, number_of_decimals=0 ) print( "Device address:", value ) value = device.read_register(registeraddress=BAUDRATE_REG_ADDR, functioncode=FUNC_CODE, number_of_decimals=0 ) print( "Baudrate:", 2400*(1&lt;&lt;int(value)) ) time.sleep(1.0) while True: try: for param in params: value = device.read_long(registeraddress=param["reg_addr"], functioncode=FUNC_CODE ) print( f'RegAddr=0x{hex(param["reg_addr"])[2:].zfill(4)}:', f'{param["name"]:&gt;4s},', f'{value/1000.0:7.3f} ', f'[{param["unit"]}]' ) print(40*'-') except IOError: print("Failed to read from the device!") time.sleep(2.0)</code></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-5.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างข้อความเอาต์พุตเมื่อรันโค้ดตัวอย่างและเชื่อมต่อกับอุปกรณ์มิเตอร์ได้สำเร็จ</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>หากจะลองสร้าง&nbsp;<strong>GUI</strong>&nbsp;โดยใช้&nbsp;<strong>Python TkInter</strong>&nbsp;เพื่ออ่านค่าจากมิเตอร์และแสดงผล ก็มีตัวอย่างโค้ดในไฟล์&nbsp;<a href="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/main.py"><code>main.py</code></a></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/vscode_venv-6.jpg" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างการทำงานของโค้ด&nbsp;<strong>Python</strong>&nbsp;สาธิตการทำงานในรูปแบบ&nbsp;<strong>GUI</strong></p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="arduino-sketch-esp32-c3">▷&nbsp;<strong>โค้ดตัวอย่าง: Arduino Sketch (ESP32-C3)</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>โค้ด&nbsp;<strong>Arduino Sketch</strong>&nbsp;ต่อไปนี้ สาธิตการอ่านค่าจากรีจิสเตอร์บางตัวภายในมิเตอร์ไฟฟ้า และใช้สำหรับบอร์ดไมโครคอนโทรลเลอร์&nbsp;<strong>ESP32-C3</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ในตัวอย่างนี้ ได้เลือกใช้ขา&nbsp;<strong>Tx=GPIO21</strong>&nbsp;และ&nbsp;<strong>Rx=GPIO20</strong>&nbsp;สำหรับ วงจร&nbsp;<strong>Hardware Serial</strong>&nbsp;หมายเลข&nbsp;<strong>0</strong>&nbsp;ของ&nbsp;<strong>ESP32-C3</strong>&nbsp;เพื่อนำไปต่อกับโมดูล&nbsp;<strong>RS485 Transceiver</strong>&nbsp;(ตั้งค่า&nbsp;<strong>Baudrate 9600</strong>)</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ขา&nbsp;<strong>A</strong>&nbsp;และ&nbsp;<strong>B</strong>&nbsp;ของโมดูล&nbsp;<strong>RS485 Transceiver</strong>&nbsp;เชื่อมต่อด้วยสายไฟหนึ่งคู่ไปยังช่องสัญญาณ&nbsp;<strong>A</strong>&nbsp;และ&nbsp;<strong>B</strong>&nbsp;ของมิเตอร์ไฟฟ้า</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>การเชื่อมต่อกับคอมพิวเตอร์ผู้ใช้ จะใช้วิธี&nbsp;<strong>USB-CDC</strong>&nbsp;เพื่อการอัปโหลด&nbsp;<strong>Arduino Sketch</strong>&nbsp;และการรับข้อความจากบอร์ดไมโครคอนโทรลเลอร์</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ฟังก์ชัน&nbsp;<code>read_regs(...)</code>&nbsp;ใช้สำหรับการอ่านค่าจากรีจิสเตอร์ขนาด 16 บิต ได้มากกว่าหนึ่งตัว และอีกฟังก์ชัน&nbsp;<code>read_long(...)</code>&nbsp;จะใช้สำหรับการอ่านค่าจากรีจิสเตอร์ตามแอดเดรสของรีจิสเตอร์ที่ต้องการ แต่อ่านรีจิสเตอร์ 2 ตัว เพื่อให้ได้ค่าเป็น 32 บิต</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ฟังก์ชัน&nbsp;<code>calc_modbus_crc(...)</code>&nbsp;ใช้สำหรับการคำนวณค่า&nbsp;<strong>16-bit CRC</strong>&nbsp;และใช้ในการตรวจสอบความถูกต้องสำหรับเฟรมข้อมูลที่มีการส่งไปและได้รับตอบกลับมา<code class="language-c++ hljs language-c">////////////////////////////////////////////////////////////// // Date: 2024-01-25 // Board: Super-Mini ESP32-C3 // Arduino IDE: v2.2.1 // Arduino ESP32 Core: v3.0.0alpha3 ////////////////////////////////////////////////////////////// #include &lt;HardwareSerial.h&gt; // Use Hardware Serial 0 or 1 #define HW_SERIAL (0) HardwareSerial RS485( HW_SERIAL ); //#define RS485 Serial0 void setup() { Serial.begin(115200); // USB-CDC while(!Serial); Serial.flush(); RS485.begin(9600); // Set Tx/Rx pins for RS485-serial if (HW_SERIAL==0) { // Default pins for Serial0: RX=GPIO20, TX=GPIO21 RS485.setPins( 20 /*RX*/, 21 /*TX*/ ); } else { // Default pins for Serial1: RX=GPIO18, TX=GPIO19 RS485.setPins( 10 /*RX*/, 9 /*TX*/ ); } RS485.setRxTimeout(1); RS485.flush(); delay(1000); Serial.println("\n\n\n\n\n"); Serial.println("Arduino-ESP32C3 Demo..." ); Serial.println("Power Meter Reading (Model ZM194-D9Y)"); Serial.flush(); } // Calculate the CRC of a Modbus RTU response. uint16_t calc_modbus_crc(const byte* data, size_t len) { uint16_t crc = 0xFFFF; for (size_t i=0; i &lt; len; i++) { crc ^= data[i]; for (int j=0; j &lt; 8; j++) { if (crc &amp; 1) { crc &gt;&gt;= 1; crc ^= 0xA001; } else { crc &gt;&gt;= 1; } } } return crc; } // Function to read modbus registers bool read_regs( byte dev_addr, byte func_code, uint16_t start_reg_addr, size_t num_regs, byte *result, uint32_t delay_ms=1 ) { byte reg_addr_hi = (start_reg_addr &gt;&gt; 8) &amp; 0xFF; byte reg_addr_lo = start_reg_addr &amp; 0xFF; byte num_regs_hi = (num_regs &gt;&gt; 8) &amp; 0xFF; byte num_regs_lo = num_regs &amp; 0xFF; byte req_frame[8] = { dev_addr, func_code, reg_addr_hi, reg_addr_lo, num_regs_hi, num_regs_lo }; size_t req_frame_len = sizeof(req_frame); uint16_t crc = calc_modbus_crc(req_frame, req_frame_len-2 ); req_frame[req_frame_len-2] = crc &amp; 0xff; req_frame[req_frame_len-1] = (crc &gt;&gt; 8) &amp; 0xff; RS485.write( req_frame, req_frame_len ); delay( delay_ms ); size_t buf_len = 5 +(2*num_regs); byte resp_frame[ buf_len+1 ] = {0}; size_t resp_frame_len = 0; #if 1 resp_frame_len = RS485.readBytes( resp_frame, buf_len ); #else uint32_t retries = 100; while ( resp_frame_len &lt; buf_len &amp;&amp; --retries &gt; 0 ) { if ( RS485.available() ) { resp_frame[resp_frame_len++] = RS485.read(); } delay(1); } #endif if ( resp_frame_len &lt; buf_len ) { return false; // error } uint16_t crc16, expected_crc16; byte *crc_bytes = &amp;resp_frame[resp_frame_len-2]; expected_crc16 = (crc_bytes[1] &lt;&lt; 8) | crc_bytes[0]; crc16 = calc_modbus_crc(resp_frame, resp_frame_len-2); if (crc16 == expected_crc16) { memcpy( result, resp_frame, resp_frame_len ); return true; // ok } else { Serial.print("CRC error: "); Serial.print(crc16, HEX); Serial.print(" : "); Serial.println(expected_crc16, HEX); return false; // error } } bool read_long( uint8_t dev_addr, uint16_t reg_start_addr, uint32_t *value ) { uint8_t func_code = 0x03; byte result[16] = {0}; // frame buffer for response if (read_regs(dev_addr, func_code, reg_start_addr, 2, result)) { uint32_t _value = (result[3]&lt;&lt; 24) | (result[4] &lt;&lt; 16) | (result[5]&lt;&lt; 8) | result[6]; *value = _value; return true; // success } *value = 0; return false; // error } // Definitions of some read-only register addresses of // the 3-phase power meter #define UA_REG_ADDR (0x0000) // Phase-A Voltage [V] #define UB_REG_ADDR (0x0002) // Phase-B Voltage [V] #define UC_REG_ADDR (0x0004) // Phase-C Voltage [V] #define UAB_REG_ADDR (0x0006) // Line-to-Line AB Voltage [V] #define UBC_REG_ADDR (0x0008) // Line-to-Line BC Voltage [V] #define UCA_REG_ADDR (0x000A) // Line-to-Line CA Voltage [V] #define IA_REG_ADDR (0x000C) // Phase-A current [A] #define IB_REG_ADDR (0x000E) // Phase-B current [A] #define IC_REG_ADDR (0x0010) // Phase-C current [A] #define PFA_REG_ADDR (0x0022) // Phase-A power factor #define PFB_REG_ADDR (0x0024) // Phase-B power factor #define PFC_REG_ADDR (0x0026) // Phase-C power factor #define TOTAL_P_REG_ADDR (0x0018) // Total active power in kW #define TOTAL_Q_REG_ADDR (0x0020) // Total reactive power in kVAR #define TOTAL_S_REG_ADDR (0x0030) // Total apparent power in kVA #define FREQ_REG_ADDR (0x0032) // Frequency in Hz typedef struct { uint16_t reg_addr; char name[8]; char unit[8]; } param_t; param_t params[]= { { UA_REG_ADDR, "Ua", "V" }, { UB_REG_ADDR, "Ub", "V" }, { UC_REG_ADDR, "Uc", "V" }, { UAB_REG_ADDR, "Uab", "V" }, { UBC_REG_ADDR, "Ubc", "V" }, { UCA_REG_ADDR, "Uca", "V" }, { IA_REG_ADDR, "Ia", "A" }, { IB_REG_ADDR, "Ib", "B" }, { IC_REG_ADDR, "Ic", "C" }, { PFA_REG_ADDR, "PFa", "-" }, { PFB_REG_ADDR, "PFb", "-" }, { PFC_REG_ADDR, "PFc", "-" }, { TOTAL_P_REG_ADDR, "P", "kW" }, { TOTAL_Q_REG_ADDR, "Q", "kVAR"}, { TOTAL_S_REG_ADDR, "S", "kVA" }, { FREQ_REG_ADDR, "Freq", "Hz" }, }; void loop() { uint8_t dev_addr = 1; uint32_t value; int N = sizeof(params)/sizeof(param_t); int index = 0; int attempts = 1; while ( index &lt; N ) { param_t *p = &amp;params[index]; if ( read_long(dev_addr, p-&gt;reg_addr, &amp;value) ) { Serial.printf( "RegAddr=0x%04X: %4s, %8.3f [%s] (#%d)\n", p-&gt;reg_addr, p-&gt;name, value/1000.0f, p-&gt;unit, attempts ); index++; attempts = 1; } else { attempts++; } } Serial.println("======================================="); Serial.flush(); delay(2000); } //////////////////////////////////////////////////////////////</code></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://iot-kmutnb.github.io/blogs/sensors/power_meter_zm194-d9y/esp32c3_demo.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>รูป: ตัวอย่างข้อความเอาต์พุตจากการทำงานของโค้ด&nbsp;<strong>Arduino Sketch</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>เมื่ออ่านค่าจากมิเตอร์ได้ถูกต้องแล้ว ก็มีตัวอย่างแนวทางการนำไปประยุกต์ใช้งานต่อไป เช่น การส่งข้อมูลไปยังระบบ&nbsp;<strong>IoT Cloud</strong>&nbsp;เช่น เลือกใช้โพรโทคอล&nbsp;<strong>MQTT</strong>&nbsp;ผ่าน&nbsp;<strong>WiFi</strong>&nbsp;หรือการบันทึกข้อมูลพร้อมวันเวลาลงในการ์ดหน่วยความจำ และการแสดงผลข้อมูลเชิงกราฟิกที่ได้จากการบันทึกในแต่ละช่วงเวลา</p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="_2">▷&nbsp;<strong>กล่าวสรุป</strong></h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>บทความนี้ได้นำเสนอการทดลองใช้งานเพาเวอร์มิเตอร์สามเฟสแบบดิจิทัล รุ่น&nbsp;<strong>ZM194-D9Y (Brand: ZJZM)</strong>&nbsp;และนำเสนอโค้ดตัวอย่างเพื่อใช้งานบอร์ดไมโครคอนโทรลเลอร์&nbsp;<strong>Arduino ESP32-C3</strong>&nbsp;และเปรียบเทียบกับการโค้ด&nbsp;<strong>Python</strong>&nbsp;ให้สามารถอ่านค่าจากมิเตอร์ได้ โดยเชื่อมต่อผ่านบัส&nbsp;<strong>RS485</strong>&nbsp;และใช้โพรโทคอล&nbsp;<strong>Modbus RTU</strong>&nbsp;เพื่อส่งเฟรมข้อมูลสำหรับคำสั่งและข้อมูลตอบกลับ</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>บทความที่เกี่ยวข้อง</strong></p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li><a href="https://iot-kmutnb.github.io/blogs/electronics/rs485_modules/">การใช้งานโมดูลสื่อสาร <strong>RS485 Transceiver</strong></a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a href="https://iot-kmutnb.github.io/blogs/sensors/xy-md02/">การใช้งานโมดูล <strong>XY-MD02 Temperature &amp; Humidity Sensor (RS485 Modbus RTU)</strong></a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a href="https://iot-kmutnb.github.io/blogs/sensors/power_meter_cj-3d3ys/">การอ่านค่าจากเพาเวอร์มิเตอร์ไฟฟ้าสามเฟส: <strong>CJ-3D3YS (ZGCJ)</strong></a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:paragraph -->
<p><em>This work is licensed under a</em>&nbsp;<strong><em>Creative Commons Attribution-ShareAlike 4.0 International License</em></strong>.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Created: 2024-01-27 | Last Updated: 2025-03-01</p>
<!-- /wp:paragraph -->

<!-- wp:separator -->
<hr class="wp-block-separator has-alpha-channel-opacity"/>
<!-- /wp:separator -->

<!-- wp:paragraph -->
<p>Copyright © 2021-2026 IoT Engineering Education, Bangkok/Thailand</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Documentation built with&nbsp;<a href="https://www.mkdocs.org/">MkDocs</a>.</p>
<!-- /wp:paragraph -->
<!-- wp:paragraph {"align":"center"} -->
<p class="has-text-align-center"><a href="https://github.com/ColinRobbins/ha-hildebrandglow-dcc">https://github.com/ColinRobbins/ha-hildebrandglow-dcc</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"align":"center"} -->
<p class="has-text-align-center"><a href="https://github.com/ColinRobbins/ha-hildebrandglow-dcc">https://github.com/alryaz/hass-mosenergosbyt</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"align":"center"} -->
<p class="has-text-align-center"><a href="https://github.com/lawrencefoley/evergy">https://github.com/lawrencefoley/evergy</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"align":"center"} -->
<p class="has-text-align-center"><a href="https://github.com/trvqhuy/nestup_evn">https://github.com/trvqhuy/nestup_evn</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a href="https://github.com/u9n/dlms-cosem">https://github.com/u9n/dlms-cosem</a></p>
<!-- /wp:paragraph -->
