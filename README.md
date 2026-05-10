<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>إدارة بئر المياه - V7</title>
    <style>
        :root {--primary: #1e40af; --success: #16a34a; --danger: #dc2626; --warning: #f59e0b; --bg: #f1f5f9;}
        * { -webkit-print-color-adjust: exact!important; print-color-adjust: exact!important; color-adjust: exact!important; }
        body {font-family: 'Segoe UI', sans-serif; background-color: var(--bg); margin: 0; padding: 15px; padding-bottom: 100px; color: #1e293b;}
.container { max-width: 1100px; margin: 0 auto; }
.header {text-align: center; background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; padding: 30px 20px; border-radius: 20px; margin-bottom: 25px;}
.well-name-input {background: rgba(255,255,255,0.2); border: 2px dashed #fff; color: white; text-align: center; font-size: 28px; font-weight: bold; width: 90%; padding: 10px; border-radius: 12px; margin-bottom: 10px;}
.stats-grid {display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 25px;}
.stat-card {background: white; padding: 20px; border-radius: 15px; text-align: center; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); border-bottom: 5px solid var(--primary);}
.stat-card h3 { margin: 0; font-size: 14px; color: #64748b; }
.stat-card p { margin: 10px 0 0; font-size: 22px; font-weight: bold; color: var(--primary); }
.card {background: white; padding: 25px; border-radius: 20px; margin-bottom: 25px; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1);}
.grid {display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 20px;}
.form-group label { display: block; margin-bottom: 8px; font-weight: bold; font-size: 14px; }
        input, select {width: 100%; padding: 12px; border: 1.5px solid #e2e8f0; border-radius: 12px; font-size: 16px; box-sizing: border-box;}
.btn-group {display: flex; gap: 10px; flex-wrap: wrap; flex-direction: column;}
.btn {cursor: pointer; border: none; padding: 14px 20px; border-radius: 12px; font-weight: bold; color: white; display: flex; align-items: center; justify-content: center; gap: 8px;}
.btn-primary { background: var(--primary); }
.btn-success { background: var(--success); }
.btn-warning { background: var(--warning); color: #000; }
.btn-danger { background: var(--danger); }
.btn:hover { transform: translateY(-2px); opacity: 0.9; }
.table-container { overflow-x: auto; border-radius: 15px; border: 2px solid #1e40af; }
        table { width: 100%; border-collapse: collapse; background: white; }
        th, td { padding: 12px; border: 1px solid #94a3b8; text-align: center; }
        th { background: #1e40af; color: white; font-weight: 700; }
        tr:nth-child(even) { background: #dbeafe; }
        tfoot tr { background: #fef3c7; font-size: 16px; }
.action-btns { display: flex; gap: 8px; justify-content: center; }
.action-btn { background: none; border: none; cursor: pointer; font-size: 18px; }
.report-header { display: none; text-align: center; border-bottom: 3px double #000; margin-bottom: 15px; padding-bottom: 15px; }
.report-footer { display: none; text-align: center; margin-top: 20px; padding-top: 15px; border-top: 2px solid #1e40af; font-size: 13px; color: #1e40af; font-weight: bold; }
.no-print { display: block; }
        @media print {
            body { background: white; padding: 0; }
    .no-print { display: none!important; }
    .card { box-shadow: none; border: none; padding: 0; margin: 0; }
    .report-header,.report-footer { display: block!important; }
            #reportArea { padding: 0; }
         .table-container { border: 2px solid #1e40af; }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header no-print">
        <input type="text" id="wellName" class="well-name-input" value="بئر المياه 💧" oninput="saveWellName()">
        <p style="margin:0; font-size: 16px; opacity: 0.9;">المصمم: عبده محمد أحمد علي</p>
    </div>

    <div class="stats-grid no-print">
        <div class="stat-card"><h3>إجمالي المبالغ</h3><p id="statTotalMoney">0 ريال</p></div>
        <div class="stat-card"><h3>إجمالي الساعات</h3><p id="statTotalHours">0 ساعة</p></div>
        <div class="stat-card"><h3>عدد المزارعين</h3><p id="statFarmerCount">0</p></div>
    </div>

    <div class="card no-print">
        <div class="grid">
            <div class="form-group"><label>اسم المزارع</label><input type="text" id="farmerName" list="farmersList"><datalist id="farmersList"></datalist></div>
            <div class="form-group"><label>التاريخ</label><input type="date" id="saleDate"></div>
            <div class="form-group"><label>وقت البدء</label><input type="time" id="startTime"></div>
            <div class="form-group"><label>وقت النهاية</label><input type="time" id="endTime"></div>
            <div class="form-group"><label>سعر الساعة</label><input type="number" id="hourlyRate" value="16000"></div>
        </div>
        <div class="btn-group">
            <button id="mainBtn" onclick="saveEntry()" class="btn btn-primary">حفظ البيانات ✅</button>
            <button id="cancelBtn" onclick="cancelEdit()" class="btn btn-warning" style="display:none;">إلغاء التعديل ✖</button>
        </div>
    </div>

    <div class="card no-print" style="background: #f8fafc; border: 1px dashed #cbd5e1;">
        <h4 style="margin-top:0;">التحكم والتقارير</h4>
        <div class="grid">
            <div class="form-group">
                <label>تصفية المزارعين</label>
                <select id="filterFarmer" onchange="renderTable()"><option value="">كل المزارعين</option></select>
            </div>
            <div class="form-group">
                <label>الشهر</label>
                <input type="month" id="filterMonth" onchange="renderTable()">
            </div>
        </div>
        <div class="btn-group" style="flex-direction: row;">
            <button onclick="openPrintWindow()" class="btn btn-danger" style="flex:1;">طباعة / PDF 📄</button>
            <button onclick="openMonthlyReport()" class="btn btn-success" style="flex:1;">تقرير شهري 📅</button>
            <button onclick="backupData()" class="btn btn-warning" style="flex:1;">نسخة احتياطية 💾</button>
            <input type="file" id="importFile" onchange="importData(event)" style="display:none">
            <button onclick="document.getElementById('importFile').click()" class="btn" style="background:#64748b; flex:1;">استعادة 🔄</button>
        </div>
    </div>

    <div id="reportArea" class="card">
        <div class="report-header" id="pdfHeader">
            <h1 id="pdfWellName" style="margin:0; color:#1e40af;">بئر المياه</h1>
            <h3 id="reportTitle">كشف حساب سقي</h3>
            <p>المزارع: <span id="pdfFarmerName" style="font-weight:bold;">الكل</span></p>
            <p style="font-size: 12px; color:#64748b;">تاريخ التقرير: <span id="reportGenDate"></span></p>
        </div>
        <div class="table-container">
            <table id="mainTable">
                <thead><tr><th>التاريخ</th><th>المزارع</th><th>البداية</th><th>النهاية</th><th>المدة</th><th>المبلغ</th><th class="no-print">العمليات</th></tr></thead>
                <tbody id="tableBody"></tbody>
                <tfoot>
                    <tr style="font-weight: bold;">
                        <td colspan="4">الإجمالي:</td>
                        <td id="totalHoursFooter">0 ساعة</td>
                        <td id="totalAmount" style="color:#b91c1c;">0 ريال</td>
                        <td class="no-print"></td>
                    </tr>
                </tfoot>
            </table>
        </div>
        <div class="report-footer">
            <b>هذا التطبيق من تصميم عبده محمد أحمد علي</b><br>
            للتواصل: 776305442 أو 733174974
        </div>
    </div>
</div>

<script>
    let salesData = JSON.parse(localStorage.getItem('well_v6_data')) || [];
    let wellName = localStorage.getItem('well_name') || 'بئر المياه 💧';
    let editId = null;
    document.getElementById('wellName').value = wellName;
    document.getElementById('saleDate').valueAsDate = new Date();
    document.getElementById('filterMonth').value = new Date().toISOString().slice(0,7);

    function saveWellName() { wellName = document.getElementById('wellName').value; localStorage.setItem('well_name', wellName); }
    function to12Hour(t) { if(!t) return ''; let [h,m]=t.split(':').map(Number); let p=h>=12?'م':'ص'; h=h%12||12; return `${h}:${m.toString().padStart(2,'0')} ${p}`; }
    function timeToMinutes(t) { let [h,m]=t.split(':').map(Number); return h*60+m; }

    function calculateDuration(s,e){
        let startMin = timeToMinutes(s);
        let endMin = timeToMinutes(e);
        if(endMin <= startMin) endMin += 1440;
        let f = endMin - startMin;
        return{text:Math.floor(f/60)+"س "+(f%60)+"د",decimal:f/60,startMin,endMin}
    }

    function checkOverlap(date, start, end, excludeId=null) {
        const newStart = timeToMinutes(start);
        let newEnd = timeToMinutes(end);
        if(newEnd <= newStart) newEnd += 1440;

        if((newEnd - newStart) > 1440) {
            return {overlap: true, reason: 'long', hours: ((newEnd - newStart)/60).toFixed(1)};
        }

        const sameDayData = salesData.filter(x => x.date === date && x.id!== excludeId);

        for(let item of sameDayData) {
            let itemStart = timeToMinutes(item.start);
            let itemEnd = timeToMinutes(item.end);
            if(itemEnd <= itemStart) itemEnd += 1440;
            if(newStart < itemEnd && newEnd > itemStart) {
                return {overlap: true, with: item};
            }
        }
        return {overlap: false};
    }

    function saveEntry(){
        const a=document.getElementById('farmerName').value.trim(),b=document.getElementById('saleDate').value,c=document.getElementById('startTime').value,d=document.getElementById('endTime').value,e=parseInt(document.getElementById('hourlyRate').value);
        if(!a||!b||!c||!d)return alert("⚠️ يرجى ملء جميع الحقول");

        const overlapCheck = checkOverlap(b, c, d, editId);
        if(overlapCheck.overlap) {
            if(overlapCheck.reason === 'long') {
                return alert(`❌ المدة طويلة جداً: ${overlapCheck.hours} ساعة!\nالحد الأقصى 24 ساعة.`);
            }
            return alert(`❌ تداخل في الوقت!\nالبئر محجوز من ${to12Hour(overlapCheck.with.start)} إلى ${to12Hour(overlapCheck.with.end)} للمزارع: ${overlapCheck.with.farmer}\n\nاختر وقت ثاني.`);
        }

        if(editId){
            const f=salesData.findIndex(x=>x.id===editId);
            salesData[f]={...salesData[f],farmer:a,date:b,start:c,end:d,rate:e};
            cancelEdit()
        }else{
            salesData.push({id:Date.now(),farmer:a,date:b,start:c,end:d,rate:e})
        }
        localStorage.setItem('well_v6_data',JSON.stringify(salesData));
        renderTable();
        updateFarmersList();
        document.getElementById('farmerName').value='';
        document.getElementById('startTime').value='';
        document.getElementById('endTime').value='';
    }

    function editItem(id){const a=salesData.find(x=>x.id===id);if(!a)return;document.getElementById('farmerName').value=a.farmer;document.getElementById('saleDate').value=a.date;document.getElementById('startTime').value=a.start;document.getElementById('endTime').value=a.end;document.getElementById('hourlyRate').value=a.rate;editId=id;document.getElementById('mainBtn').innerText="تحديث السجل 🔄";document.getElementById('mainBtn').className="btn btn-success";document.getElementById('cancelBtn').style.display="block";window.scrollTo({top:0,behavior:'smooth'})}
    function cancelEdit(){editId=null;document.getElementById('mainBtn').innerText="حفظ البيانات ✅";document.getElementById('mainBtn').className="btn btn-primary";document.getElementById('cancelBtn').style.display="none";document.getElementById('farmerName').value=''}
    function deleteItem(id){if(confirm("❗ هل أنت متأكد من الحذف؟")){salesData=salesData.filter(x=>x.id!=id);localStorage.setItem('well_v6_data',JSON.stringify(salesData));renderTable();updateFarmersList()}}
    
    function getFilteredData(){
        const f=document.getElementById('filterFarmer').value;
        const m=document.getElementById('filterMonth').value;
        return salesData.filter(x=>{
            const farmerMatch =!f||x.farmer===f;
            const monthMatch =!m||x.date.startsWith(m);
            return farmerMatch && monthMatch;
        }).sort((a,b)=>new Date(b.date+' '+b.start)-new Date(a.date+' '+a.start))
    }

    function renderTable(){const a=document.getElementById('tableBody');a.innerHTML='';let b=0,c=0;const d=getFilteredData();d.forEach(x=>{const e=calculateDuration(x.start,x.end),f=Math.round(e.decimal*x.rate);b+=f;c+=e.decimal;a.innerHTML+=`<tr><td>${x.date}</td><td><b>${x.farmer}</b></td><td>${to12Hour(x.start)}</td><td>${to12Hour(x.end)}</td><td>${e.text}</td><td>${f.toLocaleString()} ريال</td><td class="no-print action-btns"><button class="action-btn" onclick="editItem(${x.id})">✏️</button><button class="action-btn" onclick="deleteItem(${x.id})" style="color:red;">🗑️</button></td></tr>`});document.getElementById('totalAmount').innerText=b.toLocaleString()+" ريال";document.getElementById('totalHoursFooter').innerText=Math.floor(c)+" س "+Math.round((c%1)*60)+" د";document.getElementById('statTotalMoney').innerText=b.toLocaleString()+" ريال";document.getElementById('statTotalHours').innerText=Math.floor(c)+" ساعة";document.getElementById('statFarmerCount').innerText=new Set(salesData.map(x=>x.farmer)).size}
    function updateFarmersList(){const a=[...new Set(salesData.map(x=>x.farmer))];document.getElementById('filterFarmer').innerHTML='<option value="">كل المزارعين</option>'+a.map(f=>`<option value="${f}">${f}</option>`).join('');document.getElementById('farmersList').innerHTML=a.map(f=>`<option value="${f}">`).join('')}

    function openPrintWindow(isMonthly=false) {
        const fFarmer = document.getElementById('filterFarmer').value;
        const fMonth = document.getElementById('filterMonth').value;
        document.getElementById('pdfWellName').innerText = wellName;
        document.getElementById('pdfFarmerName').innerText = fFarmer || "الكل";
        document.getElementById('reportGenDate').innerText = new Date().toLocaleString('ar-YE');
        
        if(isMonthly && fMonth) {
            const [year, month] = fMonth.split('-');
            const monthName = new Date(year, month-1).toLocaleString('ar', {month: 'long', year: 'numeric'});
            document.getElementById('reportTitle').innerText = `تقرير شهر ${monthName}`;
        } else {
            document.getElementById('reportTitle').innerText = 'كشف حساب سقي';
        }

        const printContent = document.getElementById('reportArea').innerHTML;
        const printWindow = window.open('', '_blank');
        printWindow.document.write(`
            <html dir="rtl"><head><title>تقرير</title>
            <style>
                *{-webkit-print-color-adjust:exact!important;print-color-adjust:exact!important;color-adjust:exact!important;}
                body{font-family:'Segoe UI',sans-serif;padding:20px;margin:0}
                table{width:100%;border-collapse:collapse;border:2px solid #1e40af}
                th,td{padding:10px;border:1px solid #94a3b8;text-align:center}
                th{background:#1e40af;color:white;font-weight:bold}
                tr:nth-child(even){background:#dbeafe}
                tfoot tr{background:#fef3c7;font-weight:bold;font-size:16px}
                tfoot td{color:#b91c1c}
         .report-header{text-align:center;border-bottom:3px double #000;margin-bottom:15px;padding-bottom:15px}
         .report-footer{text-align:center;margin-top:20px;padding-top:15px;border-top:2px solid #1e40af;font-size:13px;color:#1e40af;font-weight:bold}
            </style>
            </head><body>${printContent}</body></html>
        `);
        printWindow.document.close();
        printWindow.focus();
        setTimeout(()=>{printWindow.print();}, 500);
    }

    function openMonthlyReport() {
        if(!document.getElementById('filterMonth').value) {
            alert('⚠️ اختر الشهر أولاً من فلتر الشهر');
            return;
        }
        openPrintWindow(true);
    }

    function backupData(){const a=new Blob([JSON.stringify(salesData)],{type:'application/json'}),b=URL.createObjectURL(a),c=document.createElement('a');c.href=b;c.download=`backup_${new Date().toISOString().slice(0,10)}.json`;c.click()}
    function importData(e){const a=e.target.files[0];if(!a)return;const b=new FileReader();b.onload=function(e){try{const c=JSON.parse(e.target.result);if(confirm("⚠️ سيتم دمج البيانات، موافق؟")){salesData=[...salesData,...c];localStorage.setItem('well_v6_data',JSON.stringify(salesData));renderTable();updateFarmersList();alert("✅ تم الاستيراد")}}catch(e){alert("❌ ملف خاطئ")}};b.readAsText(a)}

    updateFarmersList();renderTable();
</script>
</body>
</html>
