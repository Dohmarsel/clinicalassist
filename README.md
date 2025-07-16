<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Clinical Pharmacy Follow-Up Tool with AI</title>
  <!-- LIBRARIES -->
  <script src="https://js.puter.com/v2/"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js" integrity="sha512-GsLlZN/3F2ErC5ifS5QtgpiJtWd43JWSuIgh7mbzZ8zBps+dvLusV+eNQATqgA/HdeKFVgA5v3S/cIrLF7QnIg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <style>
    :root {
      --primary: #4facfe;
      --secondary: #00f2fe;
      --accent: #667eea;
      --bg: #e0f7fa;
      --success: #28a745;
      --warning: #ffc107;
      --danger: #dc3545;
    }
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, var(--bg), #ffffff);
      margin: 0;
      padding: 20px;
      font-size: 14px;
    }
    .hospital-header {
      text-align: center;
      padding: 15px 10px;
      margin-bottom: 20px;
      background-color: #f7f9fc;
      border-bottom: 4px solid var(--accent);
      box-shadow: 0 4px 12px rgba(0,0,0,0.08);
      border-radius: 16px 16px 0 0;
    }
    .hospital-header h2 {
      margin: 0;
      font-size: 2em;
      color: #00796b;
      font-weight: 600;
    }
    .hospital-header p {
      margin: 5px 0 0;
      font-size: 1.2em;
      color: #555;
    }
    .container {
      max-width: 1600px;
      margin: 0 auto;
      background: white;
      border-radius: 16px;
      box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
      padding: 30px;
    }
    h1, h3 {
      color: #00796b;
    }
    h3 {
        border-bottom: 2px solid var(--accent);
        padding-bottom: 5px;
        margin-top: 25px;
    }
    .header {
      text-align: center;
      padding: 20px;
      background: linear-gradient(to right, var(--primary), var(--secondary));
      color: white;
      border-radius: 12px;
      margin-bottom: 20px;
    }
    .case-selector {
      display: flex;
      gap: 15px;
      flex-wrap: wrap;
      margin-bottom: 20px;
      align-items: center;
    }
    .case-selector label {
      font-weight: bold;
    }
    .btn {
      background: linear-gradient(to right, var(--accent), #764ba2);
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
      transition: all 0.2s ease-in-out;
    }
    .btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }
    .btn:disabled {
        background: #ccc;
        cursor: not-allowed;
        transform: none;
        box-shadow: none;
    }
    .btn-small {
        padding: 5px 10px;
        font-size: 12px;
        margin-top: 10px;
    }
    .btn-export {
      background: linear-gradient(to right, #fa709a, #fee140);
    }
    .btn-save {
        background: linear-gradient(to right, var(--success), #20c997);
    }
    .btn-ai {
        background: linear-gradient(to right, #667eea, #764ba2);
    }
    .btn-delete {
        background: linear-gradient(to right, #ff758c, #ff7eb3);
        color: white;
        border: none;
        padding: 5px 10px;
        border-radius: 6px;
        cursor: pointer;
        font-weight: bold;
    }
    .btn-delete:hover {
        background: linear-gradient(to right, #ff6b6b, #ee5a24);
    }
    .tab-container {
      display: flex;
      flex-wrap: wrap; 
      border-bottom: 2px solid #ddd;
      margin-bottom: 10px;
      gap: 5px;
    }
    .tab {
      padding: 12px 20px;
      cursor: pointer;
      border-radius: 10px 10px 0 0;
      transition: all 0.3s ease;
      font-weight: bold;
      text-align: center;
      flex-grow: 1;
    }
    
    /* Color-coded main tabs */
    .tab:nth-child(1) { background-color: #eef2ff; color: #4338ca; }
    .tab:nth-child(1).active { background: linear-gradient(to right, #6366f1, #8b5cf6); }
    .tab:nth-child(2) { background-color: #ecfdf5; color: #059669; }
    .tab:nth-child(2).active { background: linear-gradient(to right, #10b981, #34d399); }
    .tab:nth-child(3) { background-color: #fffbeb; color: #d97706; }
    .tab:nth-child(3).active { background: linear-gradient(to right, #f59e0b, #fbbf24); }
    .tab:nth-child(4) { background-color: #f5f3ff; color: #7c3aed; }
    .tab:nth-child(4).active { background: linear-gradient(to right, #8b5cf6, #c026d3); }
    .tab:nth-child(5) { background-color: #fef2f2; color: #dc2626; }
    .tab:nth-child(5).active { background: linear-gradient(to right, #ef4444, #f87171); }

    /* Shared styles for any active tab */
    .tab.active {
      color: white;
      transform: translateY(-4px);
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }
    .tab-content { display: none; }
    .tab-content.active { display: block; }
    .section {
      margin-bottom: 30px;
      border: 1px solid #e9ecef;
      padding: 20px;
      border-radius: 10px;
      background-color: #fdfdff;
    }
    .form-group { margin-bottom: 15px; }
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: 600;
      color: #333;
    }
    input[type="text"], input[type="date"], input[type="number"], select, textarea {
      width: 100%;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 6px;
      box-sizing: border-box;
    }
    textarea { resize: vertical; }
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 15px;
    }
    .table-wrapper { overflow-x: auto; width: 100%; }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 10px;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 8px;
      text-align: left;
      color: #212529;
    }
    th {
      background-color: #f1f3f5;
      color: #212529;
      font-weight: 600;
      border-bottom: 2px solid var(--accent);
      text-align: center;
    }
    th[scope="row"] {
        background-color: #f8f9fa;
        color: #212529;
        text-align: left;
        font-weight: 600;
    }
    td input, td select { font-size: 13px; }
    .lab-group-header th {
        background-color: #e9ecef;
        color: #333;
        font-weight: bold;
        text-align: left;
    }
    /* AI Dashboard Tabbed Interface Styles */
    .ai-tab-container {
        display: flex;
        flex-wrap: wrap;
        gap: 5px;
        margin-bottom: 20px;
    }
    .ai-tab {
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 8px;
        color: white;
        font-weight: bold;
        transition: all 0.3s ease;
        flex-grow: 1;
        text-align: center;
        border: 2px solid transparent;
    }
    .ai-tab:hover { transform: translateY(-2px); }
    .ai-tab.active {
        transform: scale(1.05);
        box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        border-color: #fff;
    }
    .ai-tab:nth-child(1) { background: linear-gradient(to right, #6a11cb, #2575fc); }
    .ai-tab:nth-child(2) { background: linear-gradient(to right, #00c9ff, #92fe9d); }
    .ai-tab:nth-child(3) { background: linear-gradient(to right, #f2709c, #ff9472); }
    .ai-tab:nth-child(4) { background: linear-gradient(to right, #ffb347, #ffcc33); }
    .ai-tab:nth-child(5) { background: linear-gradient(to right, #43e97b, #38f9d7); }
    .ai-tab:nth-child(6) { background: linear-gradient(to right, #00b09b, #96c93d); }
    .ai-tab:nth-child(7) { background: linear-gradient(to right, #4776E6, #8E54E9); }

    .ai-tab-panel {
        display: none;
        padding: 20px;
        background-color: #fdfdff;
        border: 1px solid #e9ecef;
        border-radius: 10px;
    }
    .ai-tab-panel.active { display: block; }
    .ai-tab-panel pre {
        white-space: pre-wrap;
        word-wrap: break-word;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        font-size: 15px;
        line-height: 1.7;
        color: #333;
    }
    .loading {
        text-align: center;
        padding: 50px;
        color: #667eea;
    }
    .spinner {
        border: 4px solid #f3f3f3;
        border-top: 4px solid #667eea;
        border-radius: 50%;
        width: 40px;
        height: 40px;
        animation: spin 1s linear infinite;
        margin: 20px auto;
    }
    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }
    .error-message {
        background: linear-gradient(135deg, #ff6b6b, #ee5a24);
        color: white;
        padding: 15px;
        border-radius: 10px;
        margin: 20px 0;
        border-left: 5px solid #ffffff;
    }
    .case-manager-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 12px;
        border: 1px solid #e0e0e0;
        border-radius: 8px;
        margin-bottom: 8px;
        background-color: #f9fafb;
    }
    .case-manager-item span {
        font-weight: bold;
        color: #333;
    }
    @media (max-width: 768px) {
        .ai-tab-container { flex-direction: column; }
    }
  </style>
</head>
<body>
<div class="hospital-header">
  <h2> Clinical Decision Support System</h2>
  <p>قسم الصيدلة الاكلينكية</p>
</div>
<div class="container">
  <div class="header">
    <h1>Clinical Pharmacy Follow-Up Tool with AI Analysis</h1>
    <p>خدمة صحية أفضل للجميع</p>
  </div>
  <div class="case-selector">
    <label for="caseSelect">Select Case:</label>
    <select id="caseSelect" onchange="loadCase()">
      <option value="">-- Select Existing Case --</option>
    </select>
    <input type="text" id="newCaseId" placeholder="Enter New Case ID" />
    <button class="btn" onclick="createNewCase()">Create New Case</button>
    <select id="aiProviderSelect" style="padding: 8px; border-radius: 6px; border: 1px solid #ccc;">
        <option value="groq">AI: Groq (Llama 3)</option>
        <option value="puter">AI: Puter (OpenAI GPT-3.5)</option>
    </select>
    <button class="btn btn-ai" onclick="generateAIReport()" id="aiReportBtn" disabled>⚡ Generate AI Report</button>
  </div>
  
  <datalist id="symptoms-list"></datalist>
  <datalist id="drugs-list"></datalist>

  <div id="caseForm" style="display:none;">
    <form id="mainForm">
        <div class="tab-container">
            <div class="tab active" onclick="switchTab('demographics')">Patient & Clinical Info</div>
            <div class="tab" onclick="switchTab('medRecon')">Med Reconciliation</div>
            <div class="tab" onclick="switchTab('followUp')">Follow-Up Tracking</div>
            <div class="tab" onclick="switchTab('aiDashboard')">AI Dashboard</div>
            <div class="tab" onclick="switchTab('manageCases')">🗂️ Manage Cases</div>
        </div>
        <div id="demographics" class="tab-content active">
          <div class="section">
            <h3>👤 Patient Information</h3>
            <div class="grid">
              <div class="form-group"><label>Serial No.</label><input type="text" id="serialNo" /></div>
              <div class="form-group"><label>Name</label><input type="text" id="patientName" /></div>
              <div class="form-group"><label>Patient ID</label><input type="text" id="patientId" /></div>
              <div class="form-group"><label>Date of Admission</label><input type="date" id="dateOfAdmission" /></div>
              <div class="form-group"><label>Department</label><input type="text" id="department" /></div>
              <div class="form-group"><label>Bed No.</label><input type="text" id="bedNo" /></div>
              <div class="form-group"><label>Age</label><input type="number" id="age" /></div>
              <div class="form-group"><label>Sex</label><select id="sex"><option value="">Select</option><option value="Male">Male</option><option value="Female">Female</option></select></div>
              <div class="form-group"><label>Weight (kg)</label><input type="number" step="0.1" id="weight" /></div>
              <div class="form-group"><label>Height (cm)</label><input type="number" id="height" /></div>
              <div class="form-group"><label>Allergy</label><select id="allergy"><option value="No">No</option><option value="Yes">Yes</option></select></div>
              <div class="form-group"><label>Allergy Details</label><input type="text" id="allergyDetails" list="drugs-list"/></div>
              <div class="form-group"><label>Smoking</label><select id="smoking"><option value="No">No</option><option value="Yes">Yes</option></select></div>
              <div class="form-group"><label>Lactation</label><select id="lactation"><option value="No">No</option><option value="Yes">Yes</option></select></div>
              <div class="form-group"><label>Discharge Date</label><input type="date" id="dischargeDate" /></div>
              <div class="form-group"><label>Discharge Status</label><select id="dischargeStatus"><option value="">Select</option><option value="Home">Home</option><option value="Died">Died</option><option value="Transferred">Transferred</option></select></div>
            </div>
          </div>
          <div class="section">
            <h3>🏥 Clinical Information</h3>
            <div class="form-group"><label>Chief Complaints</label><textarea id="chiefComplaints" rows="4" list="symptoms-list"></textarea></div>
            <div class="form-group"><label>Past Medical History</label><textarea id="pastMedicalHistory" rows="4"></textarea></div>
          </div>
          <button type="button" class="btn btn-save" onclick="saveCase()">💾 Save Info</button>
        </div>
        <div id="medRecon" class="tab-content">
          <div class="section">
            <h3>💊 Medication Reconciliation (On Admission)</h3>
            <div class="form-group"><label>Information Source</label><input type="text" id="medReconSource" /></div>
            <div class="table-wrapper">
              <table id="medReconTable">
                <thead><tr><th>Drug Name</th><th>Dose/Route/Freq</th><th>Last Time Taken</th><th>Continue</th><th>D.C./Cause</th><th>Modified</th><th>Action</th></tr></thead>
                <tbody id="medReconRows"></tbody>
              </table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('medReconRows', 'medRecon')">+ Add Row</button>
          </div>
          <button type="button" class="btn btn-save" onclick="saveCase()">💾 Save Med Rec</button>
        </div>
        <div id="followUp" class="tab-content">
          <div class="section">
            <h3>📝 Problem List & Plan of Care</h3>
            <div class="table-wrapper">
              <table><thead><tr><th>Problem</th><th>Assessment</th><th>Plan of Care</th><th>Action</th></tr></thead><tbody id="problemListRows"></tbody></table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('problemListRows', 'problemList')">+ Add Row</button>
          </div>
          <div class="section">
            <h3>📈 Vital Signs & Monitoring</h3>
            <div class="table-wrapper">
              <table id="vitalsTable">
                <thead><tr><th>Vital Signs</th><th>O/A</th><th>Follow-up 1</th><th>Follow-up 2</th><th>Follow-up 3</th><th>Follow-up 4</th><th>Follow-up 5</th></tr></thead>
                <tbody id="vitalsRows"></tbody>
              </table>
            </div>
          </div>
          <div class="section">
            <h3>🔬 Laboratory Data</h3>
            <div class="table-wrapper">
              <table><thead><tr><th>Tests</th><th>Normal</th><th>Follow-up 1</th><th>Follow-up 2</th><th>Follow-up 3</th><th>Follow-up 4</th><th>Follow-up 5</th></tr></thead><tbody id="labRows"></tbody></table>
            </div>
          </div>
          <div class="section">
            <h3>💉 Medication Administration Record</h3>
            <h4>Infusion Table</h4>
            <div class="table-wrapper">
              <table><thead><tr><th>Drug</th><th>Dilution</th><th>Follow-up 1</th><th>Follow-up 2</th><th>Follow-up 3</th><th>Follow-up 4</th><th>Follow-up 5</th><th>Action</th></tr></thead><tbody id="infusionRows"></tbody></table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('infusionRows', 'infusion')">+ Add Infusion</button>
            <h4 style="margin-top:20px;">Antibiotics</h4>
            <div class="table-wrapper">
              <table><thead><tr><th>Drug/Route</th><th>Dose/Freq/Dilution</th><th>Follow-up 1</th><th>Follow-up 2</th><th>Follow-up 3</th><th>Follow-up 4</th><th>Follow-up 5</th><th>Action</th></tr></thead><tbody id="antibioticsRows"></tbody></table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('antibioticsRows', 'antibiotics')">+ Add Antibiotic</button>
            <h4 style="margin-top:20px;">Oral/IV/SC/Transdermal/Others</h4>
            <div class="table-wrapper">
              <table><thead><tr><th>Drug</th><th>Dose/Freq/Route</th><th>Follow-up 1</th><th>Follow-up 2</th><th>Follow-up 3</th><th>Follow-up 4</th><th>Follow-up 5</th><th>Action</th></tr></thead><tbody id="otherMedsRows"></tbody></table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('otherMedsRows', 'otherMeds')">+ Add Other Med</button>
          </div>
          <div class="section">
            <h3>🗒️ Daily Follow-Up (Progress Notes)</h3>
            <div class="table-wrapper">
              <table><thead><tr><th>Date</th><th>Problem / Question</th><th>Recommendations</th><th>Acceptance</th><th>References</th><th>Action</th></tr></thead><tbody id="progressNotesRows"></tbody></table>
            </div>
            <button type="button" class="btn btn-small" onclick="addRow('progressNotesRows', 'progressNotes')">+ Add Note</button>
          </div>
          <button type="button" class="btn btn-save" onclick="saveCase()">💾 Save Follow-Up</button>
        </div>
        <div id="aiDashboard" class="tab-content">
            <div id="aiDashboardContent">
                <h3>🤖 AI-Powered Clinical Analysis</h3>
                <p>Click "Generate AI Report" to analyze the current case data. The analysis will appear here.</p>
            </div>
        </div>
        <div id="manageCases" class="tab-content">
          <div class="section">
            <h3>🗂️ Manage Saved Cases</h3>
            <p>Here you can permanently delete saved cases from your browser's storage.</p>
            <div id="case-management-list">
              <!-- Case list will be populated by JavaScript -->
            </div>
          </div>
        </div>
    </form>
  </div>
</div>

<script>
const GROQ_API_KEY = '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';
const GROQ_MODEL = 'llama3-70b-8192';

let currentCaseId = null;
let cases = JSON.parse(localStorage.getItem('pharmaCases') || '{}');

const hospitalDrugList = [...new Set([
  "5-Fluorouracil 250 mg", "5-Fluorouracil 500mg ampoule", "5- fluorouracil - Ebewe 250 mg - 5- fluorouracil 250 mg - Injection", "abacavir 120mg+lamivudine60mg", "abacavir 300mg", "Abacavir 500 mg", "Abemaciclib 50 mg -  tablet", "Abemaciclib100 mg - tablet", "Abemaciclib150 - tablet", "Abemaciclib 200 mg - tablet",
  "Abiraterone 500 mg - tablet", "Abiraterone acetate 250 mg tablet", "Acalabrutinib 100mg - capsule", "Acarbose 50 mg-tablet", "Aceclofenac 100 mg tablet", "Acetazolamide 250mg - Tablet -", "Acetyl Salicylic Acid 100mg - Cap/Tab", "Acetyl Salicylic Acid 300mg - Cap/Tab -", "Acetyl Salicylic Acid 75-81mg",
  "acetylcistein 200 mg", "Acetylcysteine 300 mg - ampoule", "Acetylcysteine 5 gm20% I.V Infusion", "Acetylcysteine 600 gm - Sachet", "Acitretin 10 mg - Tablet", "Acitretin 25 mg - Tablet", "Activated Charcoal for gas absorption 250GM", "Acyclovir 400 mg / 5 ml Suspension", "Acyclovir 400mg Tablet",
  "Acyclovir 5 % Cream", "ACYCLOVIR 200MG", "ACYCLOVIR 200MG/5ML - Suspension", "ACYCLOVIR 250MG - Vial + Solvent", "ACYCLOVIR 500MG - Vial + Solvent", "ACYCLOVIR 800MG", "Adalimumab 40 Prefilled Syringe", "Adapalene 0.1% cream tube", "ADEFOVIR 10MG - Cap/Tab", "Adrenaline 1mg/ml - Ampoule - (I.V./I.M./S.C.)",
  "Aescin 1 % + Diethylamino Salicylate 5 %", "Aescin 40mg - Tablet -", "Aflibercept 100mg / 4 ml vial", "Aflibercept 200mg / 4 ml", "Aflibercept 40mg solution for intravitreal injection", "Alcaftapro 0.25% - Eye Drops", "ALBENDAZOLE 100MG/5ML - Syrup - Bottle (20ml)", "ALBENDAZOLE 200MG - Cap/Tab -",
  "Albendazole 400mg", "alendronic acid 70 mg tablet", "Alfacalcidol 0.25mcg", "Alfacalcidol 0.5 mcg", "Alfacalcidol 1 mcg - Tablet", "Alfacalcidol 1 mcg/0.5 ml", "Alfacalcidol 2 mcg / ml oral drops", "Alfacalcidol 2 mcg/1 ml", "ALFAPROSTIN 0.5 mg/ml", "Alfuzosin Hydrochloride 10 mg - Modified release tablet",
  "Alglucerase alfa 50 mg vial", "ALHYDRAN CREAM 30 MG Tube", "Alirocumab 150mg - Pen", "Alirocumab 75mg - Pen", "Alizapride 50mg - Ampoule (2ml)", "Allopurinol 100mg - Tablet", "Allopurinol 300mg Tablet", "ALOGLIPTIN 12.5mg + METFORMIN HYDROCHLORIDE 500mg tablet", "Alogliptin 6.25 mg (DP-4 Inhibitor) Tablet",
  "ALPRAZOLAM 0.25MG - Tablet", "ALPRAZOLAM 0.5MG - Tablet -", "Alprostadil - PGE1 - Prostaglandin E1", "Alteplase 50mg - Vial -", "Aluminium hydroxide 200 + magnesium hydroxide 20 + simethicone 20 chewable tablet", "Amantadine 100 mg - capsule or tablet", "AMANTADINE 200MG/500ML - Solution for I.V. Infusion - Bottle (500ml)",
  "Ambroxol Oral drops", "Amikacin Lotion Spray -", "AMIKACIN 100MG - Vial -", "AMIKACIN 500MG - Vial -", "Amiloride 5mg + Hydrochlorothiazide 50mg - Tablet", "Amino Acid Combination Infusion (for Hepatic Patient) -", "Amino Acid Combination Infusion (for Renal Patient) - Bottle (500ml) with Rubber Cap -",
  "Amiodarone 150 mg/3 ml Ampoule", "Amiodarone 200mg tablet", "AMISULPRIDE 200MG - Tablet", "AMISULPRIDE 400MG - Tablet", "AMISULPRIDE 50MG - Tablet -", "AMITRIPTYLINE 10MG - Tablet -", "AMITRIPTYLINE 25MG - Tablet -", "AMITRIPTYLINE 50 MG - Tablet -", "Amlodipine 10 mg tablet", "Amlodipine 10mg + Indapamide 1.5mg - Film Coated Tablet",
  "Amlodipine 10mg + Indapamide 2.5mg + Perindopril 10mg - Film Coated Tablet", "Amlodipine 10mg + Olmesartan 40mg - Tablet", "Amlodipine 10mg + Olmesartan 40mg + Hydrochlorothiazide 25mg - Film Coated Tablet -", "Amlodipine 10mg + Perindopril 10mg - Tablet", "Amlodipine 10mg + Perindopril 5mg - Tablet",
  "Amlodipine 10mg + Telmisartan 80mg - Tablet", "Amlodipine 10mg + Valsartan 160mg", "Amlodipine 10mg + Valsartan 160mg + Hydrochlorothiazide 25mg - Film Coated Tablet", "Amlodipine 10mg + Valsartan 160mg tablet", "Amlodipine 5 mg Tablet", "Amlodipine 5mg + Benazepril 10mg - Capsule or Tablet",
  "Amlodipine 5mg + Bisoprolol 5mg - Tablet", "Amlodipine 5mg + Indapamide 1.25mg + Perindopril 5mg - Film Coated Tablet", "Amlodipine 5mg + Indapamide 1.5mg - Film Coated Tablet", "Amlodipine 5mg + Olmesartan 20mg - Tablet -", "Amlodipine 5mg + Olmesartan 20mg + Hydrochlorothiazide 12.5mg - Film Coated Tablet",
  "Amlodipine 5mg + Olmesartan 40mg - Tablet", "Amlodipine 5mg + Olmesartan 40mg + Hydrochlorothiazide 12.5mg - Film Coated Tablet", "Amlodipine 5mg + Olmesartan 40mg + Hydrochlorothiazide 25mg - Film Coated Tablet", "Amlodipine 5mg + Perindopril 10mg - Tablet", "Amlodipine 5mg + Perindopril 5mg - Tablet",
  "Amlodipine 5mg + Telmisartan 80mg - Tablet", "Amlodipine 5mg + Valsartan 160mg", "Amlodipine 5mg + Valsartan 160mg + Hydrochlorothiazide 12.5mg - Tablet", "Amlodipine 5mg + Valsartan 80mg - Film Coated Tablet", "AMOXICILLIN 125MG + CLAVULANIC ACID 31MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution -",
  "AMOXICILLIN 125MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution", "AMOXICILLIN 1G - Vial + Solvent -", "AMOXICILLIN 250MG + CLAVULANIC ACID 62MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution", "AMOXICILLIN 250MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution",
  "AMOXICILLIN 400MG + CLAVULANIC ACID 57MG/5ML - Suspension", "AMOXICILLIN 500MG - Cap/Tab -", "Amoxicillin 1 g + Clavulanic acid 200 mg vial", "AMOXICILLIN 500MG + CLAVULANIC ACID 125MG - Cap/Tab", "AMOXICILLIN 875MG + CLAVULANIC ACID 125MG - Cap/Tab -", "AMOXICILLIN 875MG + CLAVULANIC ACID 125MG - Sachet",
  "AMPICILLIN 1G + SULBACTAM 500MG vial", "AMPICILLIN 1G - Vial + Solvent -", "Ampicillin 125 mg + Sulbactam 125 mg suspension -", "AMPICILLIN 250MG + SULBACTAM 125MG - Cap/Tab", "AMPICILLIN 250MG + SULBACTAM 125MG - Vial + Solvent", "AMPICILLIN 250MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution",
  "AMPICILLIN 500MG - Cap/Tab -", "AMPICILLIN 500MG + SULBACTAM 250MG - Vial + Solvent -", "AMPICILLIN 500MG - Vial + Solvent -", "AMPICILLIN 2G + SULBACTAM 1G - Vial + Solvent", "Anagrelide 0.5mg - Capsule -", "Anakinra 100mg/0.67ml -Prefilled syringe", "Anastrazol 1mg - Tablet", "ANIDULAFUNGIN 100.000 mg vial 30 ml",
  "Anti RH 300mg injection", "Anticoagulant gel containing pentosan", "Apixaban 2.5mg - Film Coated Tablet", "Apixaban 5mg - Film Coated Tablet", "Aprepitant 80mg", "ARIPIPRAZOLE 10 mg", "ARIPIPRAZOLE 15MG - Tablet -", "ARIPIPRAZOLE 1MG/ML - Syrup", "ARIPIPRAZOLE 20MG - Tablet", "Aripiprazole 10 mg", "Aripiprazole 30 mg tablet",
  "ARMODAFINIL 150MG", "AROMASIN 25 mg Tablet -", "Articaine 4 % cartidge 1,200,000", "Artesunate 60 mg vial", "ASENAPINE 10MG - Sublingual", "ASENAPINE 5MG", "Ataluren 1000 mg - Sachet", "Ataluren 125 mg- Sachet", "Ataluren 250 mg- Sachet", "Atenolol 100 mg + Thiazide 25 mg", "Atenolol 100mg - Tablet",
  "Atenolol 25 mg film Coated Tablet", "Atenolol 50mg + Thiazide", "Atenolol 50mg - Tablet", "Atezolizumab 1200 mg/20ml - vial", "Atomoxetine 25mg - Cap/Tab", "ATOMOXETINE 10MG - Capsule", "ATOMOXETINE 18MG - Capsule", "ATOMOXETINE 40MG - Capsule", "ATOMOXETINE 4MG/ML - Syrup -", "ATOMOXETINE 60MG - Capsule",
  "Atorvastatin 10mg - Tablet", "Atorvastatin 20mg - Tablet -", "Atorvastatin 40mg - Tablet -", "Atorvastatin 80mg - Tablet", "Atracurium Besylate 25 mg - Atracurium Hamlen - Benzyl Alcohol Free ampule", "Atracurium Besylate 50 mg - Atracurium Hamlen - Benzyl Alcohol Free ampule", "Atropine 1 % Eye Drops", "Atropine 1mg/ml",
  "Avocado + Soybean - Tablet", "Axitinib 1 mg tablet", "Axitinib 5 mg tablet", "Azathioprine 50mg tablet", "Azelaic Acid 20 % Cream", "AZITHROMYCIN 200MG/5ML - Suspension - Bottle (15ml) + Water for reconstitution", "Azithromycin 1 % Eye Drops", "AZITHROMYCIN 250MG", "AZITHROMYCIN 500MG - Cap/Tab", "AZITHROMYCIN 500MG",
  "Baclofen 10mg - Cap/Tab -", "Baclofen 25mg - Cap/Tab -", "Bamlanlvimab  Vial", "Baricitinib 2mg - Tab", "Baricitinib 4 mg tablet", "Basiliximab 20mg - Vial + Solvent", "BCG Onco- Vial - اورام مثانة", "Beclomethasone 100 mcg / dose Inhalation", "Beclomethasone 50 mcg / dose Inhalation", "Bendamustine 100mg vial",
  "Bendamustine 25mg vial", "Benoxinate 0.4 %", "BENZATHINE PENICILLIN 1,200,000 I.U. - Vial + Solvent -", "Benzoic acid 6% + Salicylic acid 3%", "Benzoyl Peroxide 5 % Gel", "BENZTROPINE 2MG - Tablet -", "Benzyl Benzoate 20 %", "Benzyl benzoate 25 % lotion", "BENZYL PENICILLIN G SODIUM 1,000,000 I.U. - Vial + Solvent -",
  "Benzydamine 0.15% - Mouth Wash Bottle (125ml)", "Benzydamine 5 % Gel", "Bepotastine besilate 1.5% (e.d) bottle 10 ml", "Betahistine 16MG - Tablet", "Betahistine 24MG - Tablet", "Betahistine 8MG - Tablet -", "Betahistine Dihydrochloride 8 mg tablet", "Betamethasone 0 .1 % Lotion", "Betamethasone 0.05 % + Salicylic acid 2 %",
  "Betamethasone 0.1 % cream", "Betamethasone 0.1 %ointment", "Betamethasone + clioquinol - Cream", "Betamethasone + Gentamicin cream", "Betamethasone + neomycin - Cream", "Betamethasone + neomycin - ointment", "Betamethasone + Salicylic acid - Lotion 30 ml", "Betamethasone dipropionate 10 mg + Betamethasone Na PO4 mg - Ampoule/Vial",
  "betamethasone+ dexchlorpheniramine maleate - 0.25/2 mg Tablet", "Betaxolol 5 mg/ml- BETOPTIC 5 mg/ml Eye Drops", "Bevacizumab 100 mg/4 ml-avastin", "Bevacizumab 400 mg/16 ml-avastin", "Bicalutamide 50 mg", "Bimatoprost 0.03 %- BIMATOSWIX 0.03 % Eye Drops", "BIPERIDEN 2 mg Tablet", "Biperiden 5 mg / ml", "Bisacodyl 10 mg Adults Suppository",
  "Bisacodyl 5 mg Pediatric Suppository", "Bisacodyl 5mg - Tablet -", "Bisoprolol 10 mg tablet", "Bisoprolol 10mg + Hydrochlorothiazide 25mg - Tablet -", "Bisoprolol 10mg + Hydrochlorothiazide 6.25mg - Tablet", "Bisoprolol 2.5mg - Film Coated Tablet -", "Bisoprolol 2.5mg + Hydrochlorothiazide 6.25mg - Film Coated Tablet -", "Bisoprolol 5 mg tablet",
  "Bisoprolol 5 + 12.5 Hydrochlorothiazide tablet", "bisoprolol + hydrochlorothiazide", "Bleomycin 15 I.U.", "Blood Factor 7 Recombinant1MG RT", "Blood Factor 9 Recombinant 1000 I.U. Vial + Solvent", "Blood Factor 9 Recombinant 250 I.U. Vial + Solvent", "Blood Factor 9 Recombinant 500 I.U. Vial + Solvent",
  "bonixinamate  eyedrop", "Bortezomib 1 mg vial", "Bortezomib 3.5 mg - VELCADE 3.5 mg vial", "Bosentan 125mg - Film Coated Tablet - [", "Bosentan 62.5mg - Film Coated Tablet -", "brentuximab 50mg- ADCETRIS 50 mg", "Brimonidine tartrate - Eye Drops", "Brimonidine/Timolol - Eye Drops 5ml", "Brinzolamide + timolol maleate - Eye Drops",
  "Brinzolamide 1 % Eye Drops", "BROMAZEPAM 1.5MG - Tablet -", "BROMAZEPAM 3MG - Tablet -", "Bromfenac 0.9 mg - BROMOFLAM 0.09 % Eye Drops", "Bromhexine 4 mg / 5 ml syrup", "Bromhexine 4 mg/2 ml Ampoule", "Bromhexine 8 mg tablet", "Bromocriptine 2.5 mg", "Budesonide 0.02mg/ml - Bottle", "Budesonide 0.5 mg / ml", "Budesonide 160 mcg + Formoterol 4.5 mcg",
  "Budesonide 200 mcg / dose inhaler", "Budesonide 250 mcg vial", "Budesonide 32 mcg / dose Nasal Spray", "Budesonide 320 mcg + Formoterol 9 mcg", "Budesonide 400 mcg Capsule", "Budesonide 64 mcg / dose Nasal Spray", "Budesonide 80mcg + Formoterol 4.5 mcg Turbohaller 60 Doses Box/1", "Budesonide 9mg", "Bumetanide 1mg - Tablet",
  "Bupivacaine 0.5% Heavy - Ampoule (4ml) -", "Bupivacaine 0.5% - Vial (20ml) -", "bupivacain hydrochloride 5mg/ml", "Busulphan 2mg tablet", "BUSPIRONE 10MG - Tablet -", "BUSPIRONE 15MG - Tablet -", "C1 esterase inhibitor 500 IU - Cinryze 500 IU (Pack of 2 single use powder vials + 2 vials WFI each of 5ml +2 Filter transfer devices +2 disposable 10ml Syringes+2 Venipuncture sets+ 2 producti ve mats)",
  "Cabazitaxel 60 mg vial", "Cabergoline 0.5 mg -", "CAFFEINE CITRATE 20MG/ML - Vial -", "caffiene+paracetamol+phenylephrine+terpine hydrate+ viyamin C tablet", "CALAMINE 8% 120ML Lotion -", "Calcipotriol + Betamethasone Gel", "Calcipotrio + Betamethasone - ointment", "Calcium acetate 700 mg tablet", "Calcium Carbonate 1600mg - Eq. to Elemental Calcium 640mg - Tablet",
  "Calcium Chloride 10% - Ampoule (10ml) - )", "Calcium Citrate + Magnesium Citrate tablet", "Calcium dobesilate 250 mg", "Calcium Gluconate + Calcium Levulinate - Ampoule", "Calcium Gluconate + Dextrose + Potassium Chloride + Sodium Chloride", "Calcium Leucovorin = Calcium Folinate = Folinic Acid vial",
  "Calcium Polystyrene - (500g)", "Candesartan 16mg", "Candesartan 16mg + Hydrochlorothiazide 12.5mg - Tablet", "Candesartan 32mg - Tablet -", "Candesartan 32mg + Hydrochlorothiazide 12.5mg - Tablet", "Candesartan 32mg + Hydrochlorothiazide 25mg - Tablet", "Candesartan 4mg - Tablet - [", "Candesartan 8mg - Tablet -",
  "Candesartan Cilexetil 16 mg", "Candesartan Cilexetil 4 mg", "Candesartan Cilexetil 8 mg", "Capecitabine 500 mg - Tablet", "Captopril 25mg - Tablet", "Captopril 50mg + HydroChlorothiazide 25mg Tablet", "Captopril 50mg + Indapamide 2.5 tablet", "Captopril 50mg - Tablet -", "CARBAMAZEPINE 100MG/5ML - Syrup - Bottle (100ml) -",
  "CARBAMAZEPINE 200MG CT Tablet", "CARBAMAZEPINE 200MG - Tablet", "CARBAMAZEPINE 400MG C.R. - Film Coated Tablet -", "Carbamide Peroxide Ear Drops", "Carbidopa 25 mg + Levodopa 250 mg - Tablet", "Carbimazole 5 mg Tablet -", "Carbocysteine 120 mg / 5 ml ( syrup)", "Carbocysteine 250 mg / 5 ml adult Syrup",
  "Carbocysteine 375 mg Capsules -", "Carboplatin ) 450 mg / 45 ml vial", "Carboplatin 150 mg / 15 ml", "Carglumic acid 200mg - Tablets", "Carfilzomib 60mg - Vial+Solvent", "Carvedilol 12.5mg - Tablet - [", "Carvedilol 12.5mg + Ivabradine 5mg - Tablet", "Carvedilol 12.5mg + Ivabradine 7.5mg - Film Coated Tablet",
  "Carvedilol 25mg - Tablet - [", "Carvedilol 6.25mg - Tablet - [", "Carvedilol 6.25mg + Ivabradine 5mg", "Carvedilol 6.25mg + Ivabradine 7.5mg - Film Coated Tablet", "CASPOFUNGIN 50MG - Vial + Solvent - [", "CASPOFUNGIN 70MG - Vial + Solvent - [", "CEFACLOR 125MG - Suspension - Bottle (80ml) + Water for reconstitution - [",
  "CEFACLOR 250MG - Suspension - Bottle (80ml) + Water for reconstitution -", "CEFADROXIL 125MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution -", "CEFADROXIL 1G - Cap/Tab - [", "CEFADROXIL 250MG/5ML - Suspension - Bottle (80ml) + Water for reconstitution -", "Cefadroxil 500 mg / 5 ml", "CEFADROXIL 500MG - Cap/Tab - [",
  "CEFALEXIN 1G - Cap/Tab - [", "CEFALEXIN 250MG - Suspension - Bottle (80ml) + Water for reconstitution - [", "Cefalexin 250 mg", "CEFALEXIN 500MG - Cap/Tab - [", "CEFAZOLIN 1G - Vial + Solvent -", "CEFAZOLIN 500MG - Vial + Solvent -", "CEFDINIR 125MG - Suspension - Bottle (60ml)", "CEFDINIR 250MG - Suspension", "CEFDINIR 300MG - Cap/Tab",
  "CEFEPIME 1G - Vial + Solvent", "CEFEPIME 2G - Vial + Solvent -", "CEFEPIME 500MG - Vial + Solvent - [", "CEFIXIME 100MG/5ML - Suspension - Bottle (30ml) + Water for reconstitution -", "CEFIXIME 200MG - Cap/Tab -", "CEFIXIME 400MG - Cap/Tab -", "CEFOPERAZONE 1G + SULBACTAM 1G - Vial + Solvent", "CEFOPERAZONE 1G + SULBACTAM 500MG - Vial + Solvent -",
  "CEFOPERAZONE 1G - Vial + Solvent -", "CEFOPERAZONE 2G - Vial + Solvent -", "CEFOPERAZONE 500MG - Vial + Solvent -", "cefotaxime 1 gm - vial+ solvent", "CEFOTAXIME 250MG - Vial + Solvent -", "CEFOTAXIME 2G - Vial + Solvent -", "CEFOTAXIME 500MG - Vial + Solvent -", "CEFOXITIN 1G (I.V.) - Vial + Solvent", "CEFTAROLIN 600MG - Vial + Solvent",
  "CEFTAZIDIME 0.5 gm- CEFIDIME 0.5 gm Vial + Solvent", "CEFTAZIDIME 1G - Vial + Solvent -", "CEFTAZIDIME 250MG - Vial + Solvent", "CEFTAZIDIME 2G + AVIBACTAM 0.5MG - Vial + Solvent -", "CEFTAZIDIME 500MG - Vial + Solvent -", "Ceftriaxone 1 g (IV) vial", "CEFTRIAXONE 1G (I.M.) - Vial", "CEFTRIAXONE 2G - Vial + Solvent",
  "CEFTRIAXONE 500MG (I.V.) - Vial + Solvent - [", "Ceftriaxone 500 mg (IM) vial", "Cefuroxime 500 mg Capsule or Tablet", "Celecoxib 100mg - Cap/Tab -", "CELECOXIB 200 mg Capsule", "CEPHRADINE 1G - Cap/Tab -", "CEPHRADINE 1G - Vial + Solvent - [", "CEPHRADINE 500MG - Vial + Solvent - [", "Cephradine 250 mg suspension",
  "Cephradine 500 mg capsule", "CEREBROLYSIN 215.2MG/ML (1ML) - Ampoule (1ml) - [Cerebrolysin (", "CEREBROLYSIN 215.2MG/ML (5ML) - Ampoule (5ml) - [Cerebrolysin (Ever Neuro Pharma)]", "Certolizumap pegol 200mg/ml prefilled syring-cimzia", "Cetalkonium + Choline Salicylate Oral Gel", "Cetirizine 10 mg", "Cetrizine Syrup",
  "Cetrizine Tablet", "Cetuximab 5 mg / ml for infusion (100mg)", "Chlorambucil 2mg tablet-leukeran", "Chloramphenicol 0.2 % + Prednisolone 0.5 % Eye Drops", "Chloramphenicol 0.5 % Eye Drops", "Chloramphenicol 1 % Ointment", "Chlordiazepoxide 5mg + Clidinium Bromide 2.5mg - Tablet", "Chlorobutanol + Phenazone Ear Drops-otocalm",
  "CHLOROQUINE 250MG - Cap/Tab -", "Chlorpheniramine 2 mg / 5 mlSyrup", "Chlorpheniramine 4 mg", "Chlorpheniramine 5 mg Injection", "CHLORPROMAZINE 100MG - Tablet -", "CHLORPROMAZINE 25MG - Tablet -", "chlorzoxazone +diclofenac 50mg", "Cholecalciferol (Vit. D3) 1000 IU", "Cholecalciferol (Vit. D3) 200000 I.U. / ml",
  "Cholecalciferol (Vit. D3) 2800 IU / ml", "Chromium Picolanate + Garcinia Cambogia Fruit Extract - Tablet", "Chymotrypsin + Trypsin ampoule", "Chymotrypsin + Trypsin tablet", "Ciclesonide 160 mg inhaler", "Ciclesonide 80 mcg/dose - inhaler", "Cilostazol 100mg tablet", "Cilostazol 50mg - Tablet", "Cinacalcet 30 mg tablet-mimpara",
  "Cinacalcet 60 mg tablet", "CINNARIZINE 20MG + DIMENHYDRINATE 40MG - Tablet -", "CINNARIZINE 25MG - Tablet", "CIPROFLOXACIN + DEXAMETHASONE bottle 15 ml", "Ciprofloxacin 0.3 % Eye drops", "Ciprofloxacin 0.3 % Eye Ointment", "Ciprofloxacin 0.3 %Eye Ointment", "CIPROFLOXACIN 1G - Extended Release Tablet - [",
  "CIPROFLOXACIN 200MG - Vial - [", "CIPROFLOXACIN 250MG - Cap/Tab - [", "CIPROFLOXACIN 500MG - Cap/Tab - [", "CIPROFLOXACIN 500MG + METRONIDAZOLE 500MG - Cap/Tab", "CIPROFLOXACIN 750MG - Cap/Tab -", "Cisatracurium 2mg/ml - Ampoule (10ml) -", "Cisatracurium 2mg/ml - Ampoule (5ml) -", "Cisplatin 10 mg vial",
  "Cisplatin 50 mg", "CITALOPRAM 2MG/ML - Syrup - Bottle (120ml) -", "CITALOPRAM 40MG - Tablet -", "Citalopram 20 mg", "CITICOLINE 100MG/ML - Oral Drops", "CITICOLINE 500MG - Ampoule", "Cladribine 10mg - amp", "Cladribine 10mg - Tablet", "CLARITHROMYCIN 125MG/5ML - Suspension - Bottle", "CLARITHROMYCIN 250 mg susp bottle",
  "CLARITHROMYCIN 250MG - Cap/Tab", "CLARITHROMYCIN 250MG + OMEPRAZOLE 20MG + TINIDAZOL 500MG - Cap/Tab", "CLARITHROMYCIN 500MG - Cap/Tab", "CLARITHROMYCIN 500MG - Vial + Solvent", "CLINDAMYCIN 150MG - Cap/Tab -", "Clindamycin 1 % - Topical solution", "Clindamycin 300MG", "CLINDAMYCIN 300MG - Cap/Tab", "CLINDAMYCIN 300MG", "CLINDAMYCIN 600MG - Ampoule",
  "Clindamycin vaginal cream 2 %", "Clobetasol 0.05 % cream", "Clobetasol 0.05 % oint", "CLOMIPHENE 50 mg", "CLOMIPRAMINE 25MG - Anafronil - Supranil", "CLOMIPRAMINE 75MG - Tablet", "CLONAZEPAM 0.5MG - Tablet", "CLONAZEPAM 2.5MG/ML - Oral Drops - Bottle (15ml)", "CLONAZEPAM 2MG - Tablet", "clopidogrel 75 mg tablet",
  "Clopixol Acuphase  100MG - Ampoule", "Clopixol Depot  200MG - Ampoule", "Clotrimazole 1 % + Corticosteroid", "Clotrimazole 1 % Cream- Tube (15gm)", "Clotrimazole 1 %Lotion", "Clotrimazole 1 % spray", "Clotrimazole 100 mg vag.tablet", "Clotrimazole 500 mgVaginal Tablet", "CLOZAPINE 100MG - Tablet", "CLOZAPINE 25MG - Tablet",
  "Coartem 20 mg  (Artemether 20mg+ Lumefantrine 120mg) tablet", "COENZYME Q-10 - Capsule", "Colchicine 0.5mg", "Colistimethate Sodium 1,000,000 IU vial", "Collagen + Vitamin C Sachet", "Collagenase ointment", "colomycin 1000000 iu", "Condom", "Crizotinib 250mg capsule-xalkori", "Cyclobenzaprine 10mg - Cap/Tab",
  "Cyclobenzaprine 30mg - Cap/Tab -", "Cyclobenzaprine 5mg - Cap/Tab", "CYCLIZINE 50MG/ML - Ampoule -", "Cyclopentolate Hydrochloride Bottle 10 ml Box/1 -Eye Drop", "Cyclophosphamide 200 mg Vial", "Cyclophosfamide 1gm - Vial", "Cyclophosphamide 50 mg", "Cyclophrine 5 ml - Eye Drops 5 ml", "Cyclosporin 100 mg / mlSyrup",
  "Cyclosporin 250 mg Amp", "Cyclosporin 50 mg Amp", "Cyclosporin Preformed Microemulsion 100 mg - For Autoimmune", "Cyclosporin Preformed Microemulsion 25 mg - For Autoimmune", "Cyclosporin Preformed Microemulsion 50 mg - For Autoimmune", "Cytarabine 1 gm Vial", "Cytarabine 100 mg ampoule", "Dabigatran 110 mg - Capsule pradaxa",
  "Dabigatran 150 mg - Capsule pradaxa", "Dabigatran Etexilate 75mg - Capsule", "DACARBAZINE 200 mg Vial", "Daclatasvir 60mg -", "Dactinomycin 0.5 mg vial", "DALFAMPRIDINE 10MG - Tablet", "Danazol 200 mg", "Dantrolene 25mg - Cap/Tab", "DAPAGLIFLOZIN 10MG - Tablet", "DAPAGLIFLOZIN 5MG - Tablet", "DAPAGLIFLOZIN 5MG + METFORMIN 1000MG - Extended Release Tablet",
  "DAPSONE 100MG - Cap/Tab - [", "DAPSONE 50MG - Cap/Tab - [", "Daratumumab 400 mg/20 ml vial_darzalex", "darunavir/cobicistar/emtricitabine/tenofovir (symtuza)", "Darunavir 600mg Tab  ,prezista", "Dasatinib 50 mg-sprycel", "Decitabine 50 mg vial", "Deferasirox 125mg tablet", "Deferasirox 180mg - Tablet", "Deferasirox 250 mg tablet",
  "Deferasirox 360mg - Tablet", "Deferasirox 500mg - Tablet", "Deferasirox 90mg - Tablet", "Deferiprone 500mg - Capsule -", "Deferoxamine 500mg ampoul", "deflazacort 30 mg tablet", "deflazacort 6 mg tablet", "Degarelix 120 mg vial-firmagon", "Degarelix 80 mg vial-firmagon", "Denosumab 120 mg vial Box/1_ xgeva", "Denosumab 60 mg",
  "Desflurane 100% - Solution for Inhalation", "Desloratidine 0.5 mg / 1 ml", "DESLORATADINE 5 mg tablet", "Desmopressin 0.1 mg/ml (nasal spray)", "Desmopressin 0.1mg (eq. to 60 mcg) tablets", "Desmopressin 0.2mg (eq. to 120 mcg) tablets", "Desogestrel 75mcg - Film Coated Tablet", "DESVENLAFAXINE 100MG - Extended Release Tablet - [",
  "DESVENLAFAXINE 50MG - Extended Release Tablet - [", "Dexamethasone 0.1% Eye Drops", "Dexamethasone 0.5 mg - Tablet", "Dexamethasone 0.5 mg / 5 ml", "Dexamethasone 0.5mg/ 5ml + Chlorpheniramine maleate 2 ml / 5ml - (syrup)", "Dexamethasone 1 mg + Neomycin 3.5 mg + Polymyxin 6000", "Dexamethasone 700 mcg intravitreal applicator",
  "Dexamethasone 8 mg tablet", "Dexamethasone 8 mg/2ml", "DEXMEDETOMIDINE 200MCG - Vial", "DEXPANTHENOL 2 mg ; Ascorbic Acid (Vitamin C) 60 mg ; BIOTIN 200 mcg ; Pyridoxine Hydrochloride syrup", "Dexapanthenol 5 % Cream", "Dexlansoprazole 30mg", "Dexlansoprazole 60 mg - Capsule", "Dextromethorphan 1 % drops", "Dextromethorphan 10 mg / 5 ml",
  "Dextromethorphan 10 mg tablet", "Dextrose + Potassium Chloride + Sodium Chloride + Sodium Lactate Infusion - Bottle (500ml) with Rubber Cap", "Dextrose 10%", "Diacerin 50mg - Cap/Tab", "DIAZEPAM 10MG - Ampoule", "DIAZEPAM 5MG - Tablet", "Diclofenac 0.1 % Eye Drops", "Diclofenac 1 %gel", "Diclofenac cholestyramine 75 mgCapsule or Tablet",
  "Diclofenac Diethylamine gel", "Diclofenac Epolamine 50mg - Cap/Tab", "Diclofenac Epolamine - Gel - Tube 50 GM", "Diclofenac K 25mg - Cap/Tab -", "Diclofenac K 2mg/ml - Suspension - Bottle (140ml) -", "Diclofenac K 50mg - Cap/Tab", "Diclofenac K 50mg - Sachet -", "Diclofenac K 75mg - Ampoule", "diclofenac K 75 mg  supp.",
  "Diclofenac Na 100mg - Suppository -", "Diclofenac Na 100mg SR - Cap/Tab", "Diclofenac Na 12.5mg - Suppository -", "Diclofenac Na 150mg MR - Cap/Tab", "Diclofenac Na 25mg - Cap/Tab -", "Diclofenac Na 25mg - Suppository -", "Diclofenac Na 50mg - Cap/Tab - Declofenac Na -", "Diclofenac Na 50mg - Suppository -",
  "Diclofenac Na 75mg - Ampoule or Vial", "Diclofenac Na 75mg - Cap/Tab", "Diclofenac Na 75mg + Lidocaine Hydrochloride 20mg - Ampoule or Vial", "Dienogest 2 mg tablet", "Diethyl Carbamazine citrate 100mg", "DIFLUPREDNATE 0.500 mg (eye emulsion) bottle 5 ml", "Digoxin 0.25mg - Tablet -", "Digoxin 0.5mg/2ml - Ampoule -",
  "Diiodohydroxyquinollone 200 mg + Pthalyl Sulfathiazole 200 mg + Streptomycin Sulfate 100 mg + Hematropine 2.5 mg - Tablet Box/10", "Diltiazem 120 mg", "Diltiazem 180 mg", "Diltiazem 60mg - Film Coated Tablet - [", "dimenhydrinate 50 mg tablet", "Dimethyl Fumarate 120 mg capsule", "Dimethyl Fumarate 240mg (+ 28 Capsules as free loading doses Dimethyl Fumarate 120mg for each new Patient) - Capsule",
  "dinoprostone E2 3 mg vag .tab", "diosmin +hesperidin +vitamin c", "Diosmin 450mg + Hesperidin 50mg", "Diosmin 600mg - Film Coated Tablet", "Dioctahedral 20% - Suspension", "Dioctahedral 3g - Sachet -", "Dipyridamole 75 mg", "Disodium clodronate 400 mg", "Disodium clodronate 60 mg / ml", "Distilled Water - Bag (1L) -",
  "Distilled Water - Bag (2L) -", "Distilled Water - Bag (3L) -", "Dobutamine 250mg/20ml -", "DOCETAXEL 10mg/ml (20mg/2ml) vial", "Docetaxel 80mg/2ml - vial", "Docusate Sodium", "Docusate Sodium 5mg/ml - Syrup - Bottle (100ml) - [", "Dolutegavir 50mg تيفيكاي  339", "Domperidone 10 mg Tablet", "Domperidone 1mg/ml - Suspension",
  "Domperidone 30 mg", "DONEPEZIL 10MG - Tablet", "DONEPEZIL 5MG - Tablet", "Dopamine 200mg/5ml - Ampoule", "Dornase Alpha 2.5 mg/2.5 ml - Ampoule", "Dorzolamide + timolol EYE DROP", "Dorzolamide 2% Eye Drops", "DOTHIEPIN 25MG - Tablet", "DOTHIEPIN 75MG - Tablet", "Doxazosin 1mg - Tablet", "Doxazosin 4mg - Tablet -",
  "Doxazosin 4mg XL - Tablet", "Doxorubicin 50 mg", "Doxorubicin-Adriamycin 20 mg/10 ml فيال", "DOXYCYCLINE 100MG - Cap/Tab", "Dulaglutide 0.75/0.5ml - TRULICITY 0.75 mg/0.5 ml pen", "Dulaglutide 1.5/0.5ml - TRULICITY 1.5 mg/0.5 ml pen", "DULOXETINE 30MG - Capsule", "DULOXETINE 60MG - Capsule", "Dupilumab 200 mg - pen",
  "Dupilumab 300 mg - pen", "Durvalumab 120 mg vial", "Durvalumab 500 mg vial", "Dutasteride 0.5mg - Capsule", "Dutasteride 0.5mg + Tamsulosin 0.4mg - Capsule", "Dydrogesterone 10 mg", "EBASTINE 10 mg tablet", "Ebastine 20mg", "Ebastine 5mg/5ml Syrup", "Edoxaban 30mg - Tablet", "Edoxaban 60mg - Tablet", "efavirenz 200mg", "Efavirenz  600mg",
  "Eltrombopag 25mg - Tablet - )revolade", "Eltrombopag 50mg - Tablet - [revolade", "Elosulfase Alfa Vial - Vimizyme 5mg", "Emicizumab 60mg - Vial-hemlibra", "Empagliflozin 10 mg tablet", "Empagliflozin5 mg / Metformin 1000mg- Film Coated Tablet", "EMPAGLIFLOZIN 25 mg tablet", "EMTRICITABINE 200MG + TENOFOVIR 300MG - Cap/Tab - [",
  "Enalapril 10 mg tablet", "Enalapril 20 mg Tablet", "Enalapril 20mg + Hydrochlorothiazide 12.5mg - Tablet - [", "Enema containing sodium phosphate", "Enoxaparin 20mg/0.2ml", "Enoxaparin 40mg/0.4ml", "Enoxaparin 60mg/0.6ml", "Enoxaparin 80mg/0.8ml", "ENTACAPONE 200MG + LEVODOPA 150MG + CARBIDOPA 37.5MG - Film Coated Tablet -",
  "ENTACAPONE 200MG + LEVODOPA 50MG + CARBIDOPA 12.5MG - Film Coated Tablet", "ENTACAPONE 200MG - Tablet", "ENTECAVIR 0.5MG", "ENTECAVIR 1MG", "Enzalutamide 40mg tablet-xtandi", "Ephedrine 25mg - Ampoule", "Ephedrine 30 mg Ampoule -", "Epinastine 0.5 mg / ml Eye Drops", "Epirubicin 10 mg", "Epirubicin 200 mg", "Epirubicin 50 mg",
  "Eplerenone 25mg - Tablet -", "Eplerenone 50mg - Film Coated Tablet", "EPOETIN ALFA 10000 I.U./ml", "EPOETIN ALFA 2000 I.U./ml", "Epoetin alfa 3000 I.U./0.3ML Prefilled syring - EPOETIN ALFA 3000 I.U./ml ;", "Epoetin alfa 4000 I.U. = Erythropoietin 4000 I.U.", "Epoetin beta 2000 I.U. - Prefilled Syringe",
  "Epoetin beta 4000 I.U. - Prefilled Syringe", "Epoetin beta 5000 I.U. For Monthly Treatment", "Erenumab 70mg/ml -[Aimovig]", "ERTAPENEM 1G - Vial + Solvent", "Erythromycin 200 mg / 5 ml", "Erythromycin 500 mg", "Erythropoietin 2000 Units)", "Erythropoietin 4000-5000 Units", "ESCITALOPRAM 20MG - Film Coated Tablet -", "Escitalopram 10 mg Film Coated Tablet",
  "Esketamine 28 Mg - Nasal Spray Device", "Eslicarbazepine Acetate 800 mg - Tablet", "ESLICARBAZEPINE 400MG - Tablet", "ESLICARBAZEPINE 600MG - Tablet", "Esomeprazole 10mg - Sachet", "Esomeprazole 20mg - Tablet", "Esomeprazole 40mg - Tablet", "Esomeprazole 40mg - Vial", "Essential Phospholipids + Vitamins", "Etanercept 25 mg - Prefilled syringe-enbrel",
  "Etanercept 50 mg", "Eteplirsen 100 mg - Vial", "Eteplirsen 500 mg - Vial", "Etesevimab Vial 4429", "Ethambutol 400mg tab", "Ethambutol 500 mg", "Ethamsylate 250mg - Ampoule", "Ethamsylate 250mg - Tablet -", "Ethamsylate 500mg - Tablet", "Ethiodized Oil Fluid - Ampoule (10ml) - [Lipiodol Ultra Fluid]", "Ethinyl Estradiol 0.03mg + (Drospirenone 3mg or Dienogest 2ml) - Film Coated Tablet",
  "ethinyl estradiol 30mcg + levonorgestrel 150mcg Tablet", "Etilefrine - Bottle (15ml) -vascon", "Etilefrine 5mg - Tablet -vascon", "Etodolac 600mg - Cap/Tab -Extended Release", "Etonogestrel 68mg Implant", "Etoposide 100 mg Vial", "Etoposide 50mg - Capsule", "Etoricoxib 120mg - Cap/Tab", "Etoricoxib 60mg - Cap/Tab", "Etoricoxib 90mg - Cap/Tab",
  "Eucalyptus oil+clove oil+almond oil+menthol+camphor+peppermint oil cream /tube", "Evening Primrose Oil 1000mg - Capsule", "Everolimus 0.25 mg-certican", "Everolimus 0.75 mg-certican", "Everolimus 10 mg", "Everolimus 5mg", "Evolocumab 140mg/ml - Prefilled Syringe-repatha", "Exemestane 25 mg - Tablet", "Ezetimibe 10mg + Atorvastatin 10mg - قرص - [Atoreza - Ezastatin] (UPA 21/22)",
  "Ezetimibe 10mg + Atorvastatin 40mg - Tablet", "Ezetimibe 10mg + Rosuvastatin 10mg - Tablet", "Ezetimibe 10mg + Rosuvastatin 20mg - Tablet", "Ezetimibe 10mg + Rosuvastatin 5mg - Tablet", "Ezetimibe 10mg + Simvastatin 10mg - Tablet", "Ezetimibe 10mg + Simvastatin 20mg - Tablet", "Ezetimibe 10mg + Simvastatin 40mg - Tablet",
  "Ezetimibe 10mg + Simvastatin 80mg - Tablet", "Ezetimibe 10mg - Tablet", "Factor VIII 1000 IU - Vial", "Factor VIII 2000 IU - Vial", "Factor VIII 250 IU + VW Factor 190 IU vial", "Factor VIII 500 IU + VW Factor 375 IU vial", "Factor VIII Inhibitor 1000 IU (20 ml )", "Factor VIII Inhibitor 500 IU (10 ml)", "famotidine 20 mg eff tablet",
  "Famotidine 20mg - Ampoule (2ml) - [", "Famotidine 20mg - Tablet", "Famotidine 40mg Tablet", "Fampridine 10 mg - tablet", "Fat Soluble Vitamins A,D,E,K (for Adult) - Ampoule (10ml)", "Fat Soluble Vitamins A,D,E,K (for Neonate) - Ampoule", "Favipiravir 200 mg Tablets", "Febuxostat 120mg Tablet", "Febuxostat 40mg - Tablet", "Febuxostat 80 mg -Tablet",
  "Felodipine 10mg - Prolonged Release Tablet", "Felodipine 5mg + Metoprolol 47.5mg - Sustained Release Tablet", "Felodipine 5mg - Prolonged Release Tablet", "Feminine Formula Isoflavonoids - Soft Pieces", "Feminine Wash - Bottle 250ml - [DOUCHEAL N]", "Fenofibrate 145mg - Film Coated Tablet", "Fenofibrate 160mg - Tablet",
  "Fenofibrate 200mg - Capsule - [", "Fenofibrate 300mg - Capsule", "Fenofibric acid 105 mg - Tablet", "Fentanyl 0.1mg/2ml - Ampoule or Vial -", "Fentanyl 100mcg - Patch -", "FENTANYL 100MCG - Sublingual Tablet", "Fentanyl 12mcg - Patch", "FENTANYL 200MCG - Sublingual Tablet", "Fentanyl 25mcg - Patch -", "Fentanyl 50 mg - Patches",
  "Fentanyl 75mcg - Patch", "Ferrous Gluconate", "Ferrous Salts + Folic Acid - Tablet -", "Fexofenadine 120 mg tablet", "Fexofenadine 180 mg Tablets", "Fexofenadine 30 mg / 5 ml syrup", "FILGRASTIM 300 mcg/ ml Vial-geneleukim", "Finasteride 5mg - Cap/Tab", "Fingolimod 0.5mg - Capsule", "Fish oil 1200 mg + Omega 3 600 mg + Vitamin E - Capsule",
  "Flavoxate 200mg - Tablet -", "Flourescein sodium 10 % ampule", "FLUBENDAZOLE 100MG - Cap/Tab -", "FLUBENDAZOLE 100MG/5ML - Syrup - Bottle (30ml) -", "FLUCONAZOLE 150MG - Cap/Tab", "FLUCONAZOLE 200MG - Cap/Tab", "FLUCONAZOLE 200MG/100ML - Vial", "FLUCONAZOLE 200MG/5ML - Suspension - Bottle (60ml) + Water", "FLUCONAZOLE 25MG/5ML - Suspension - Bottle (70ml)",
  "FLUCONAZOLE 2MG/ML - Vial", "FLUCONAZOLE 50MG - Cap/Tab -", "Fludarabine 10 mgTablet", "Fludarabine 50 mgVial", "fludrohydrocortisone 0.1 mg - Tablet", "Flumazenil 0.5mg Ampoule", "FLUOXETINE 10MG - Capsule", "FLUOXETINE 20MG - Capsule", "FLUOXETINE 90MG - Capsule", "FLUPENTIXOL 40MG - Ampoule", "Flutamide 250 mg tablet",
  "Fluticasone 125 mcg + Formoterol 5 mcg", "Fluticasone 125 mcg + Salmeterol 25 mcg", "Fluticasone 250 mcg + Formoterol 10 mcg", "Fluticasone 250 mcg + Salmeterol 25 mcg inhaler", "Fluticasone 250 mcg + Salmeterol 50 mcg Diskus 60 doases", "Fluticasone 250 mcg + Salmeterol 50 mcg diskus", "Fluticasone 50 mcg + Formoterol 5 mcg",
  "Fluticasone 50 mcg + Salmeterol 25 mcg", "Fluticasone 50 mcgSpray", "Fluticasone 500 mcg + Salmeterol 50 mcg diskus", "Fluticasone propionate 0.5 mg/ 2ml ampoule", "Fluticasone propionate 125 mcg 60 dose inhaler", "Fluticasone propionate 250 mcg diskus", "FLUVOXAMINE 100mg tablet", "FLUVOXAMINE 50 mg tablet",
  "Folic acid 0.5-0.8 mg Tablet", "Folic Acid 5 mg tablet", "Folic Acid + Vitamin B1 + B2 + B6 + B12 Tablet", "Folinic acid 10 mg/ml - Vial 5 ml", "Fondaparinux 2.5mg - Prefilled Syringe -", "Fondaparinux 7.5mg - Prefilled Syringe", "Formaline 1 litre", "Formoterol fumarate 12 mcg inhale capsule", "FOSFOMYCIN 3G - Sachet",
  "Fosaprepitant 150mg", "Frekamix 1000ml -bag", "Frekamix 150 ml - Bag", "Frekamix 2000ml bag", "Frekamix 250ml -bag", "Frekamix 3000ml- bag", "Frekamix 500ml bag", "FRESUBIN 2 KCAL Bottle 200 ml", "Fulvestrant 250 mg injection", "Furosemide 20 mg - ampoule", "Furosemide 20 mg + Spironolactone 100 mg", "Furosemide 20 mg + Spironolactone 50 mg",
  "Furosemide 40 mg ampoule", "Furosemide 40mg - Tablet -", "Furosemide 500mg - Tablet", "Fusidic Acid 2 % Cream", "Fusidic Acid 2 % Ointment", "FUSIDIC ACID 10 mg/gm (ED) BOTTLE", "Fusidic acid + corticosteroid Cream", "GABAPENTIN 100MG - Capsule", "Gabapentin 300 mg - Tablet", "GABAPENTIN 400MG - Capsule", "GABAPENTIN 600 XR - Tablet",
  "GABAPENTIN 800MG - tablet", "GADODIAMIDE 0.5 mmol/ml", "GADODIAMIDE 0.5 mmol/ml vial", "Gadoteric Acid + Meglumine - Vial (10ml)", "Gadoteric Acid + Meglumine - Vial (20ml)", "GALANTAMINE 16MG - Capsule", "GALANTAMINE 4MG/ML - Syrup", "GALANTAMINE 8MG - Capsule", "Galcanezumab 120 mg Pen", "Galsulfase Vial - Naglazyme 5mg/5ml",
  "Ganciclovir 500mg Vial", "Gatifloxacin 0.3 % Eye drops", "Gefitinib 250 mg tablet", "GELATIN (COLLAGEN) HYDROLYSATE+VITAMIN C sachet", "Gemcitabine 1gm - vial - injection", "Gemcitabine 200mg/5ml - vial", "Gentamicin 0.1 % Cream", "Gentamicin 0.1 % Ointment", "Gentamicin 0.1 %Cream", "Gentamicin 0.3 % Eye Drops", "Gentamicin 0.3 % Eye Ointment",
  "GENTAMICIN 20MG - Ampoule", "GENTAMICIN 40MG - Ampoule", "GENTAMICIN 80MG - Ampoule", "Ginkgo Biloba 40mg/ml (OD) - Bottle", "Ginkgo Biloba 60mg - Capsule", "Ginkgo Biloba + Ginseng - Capsule", "Ginkgo biloba 40 mg - tablet", "Glibenclamide 5 mg tablet", "Gliclazide 80 mg - Tablet", "Gliclazide MR 30 mg tablet", "Gliclazide MR 60 mg tablet",
  "Glimepiride 1 mg Tablets-", "Glimepiride 2 mg Tablets -", "Glimepiride 3 mg Tablet", "Glimepiride 4 mg Tablet -", "Glimepride 2 mg + metformin 500 mg tablet", "Glucagon 1 mg / ml", "GLUCOSAMINE 500mg capsule", "Glucosamine + Chondroitin", "Glucosamine sulphate 500 mg + Ginkgo biloba leaf extract 50 mg Capsule",
  "Glucosamine Sulphate + Potassium Chloride 1000mg + Ascorbic Acid 70mg + Calcium Carbonate 249.74mg Eq. to Calcium 100mg - Tablet", "Glucose 10% Infusion - Bottle (250ml) with Rubber Cap", "Glucose 10% Infusion - Bottle (500ml) (ROM) -", "Glucose 10% Infusion - Bottle (500ml) with Rubber Cap", "Glucose 25% Infusion - Bottle (500ml) (ROM) -",
  "Glucose 25% Infusion - Bottle (500ml) with Rubber Cap -", "Glucose 5% + Potassium Chloride(KCl) 0.2% Infusion (ROM)", "Glucose 5% + Potassium Chloride(KCl) 0.2% Infusion - Bottle (500ml) with Rubber Cap", "Glucose 5% + Sodium Chloride 0.45% Infusion - Bottle (500ml) (ROM)", "Glucose 5% + Sodium Chloride 0.45% Infusion - Bottle (500ml) with Rubber Cap",
  "Glucose 5% + sodium chloride  0.45% infusion - bottle 50ml with rubber cap", "Glucose 5% + Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (250ml) with Rubber Cap", "Glucose 5% + Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (500ml) (ROM) -", "Glucose 5% + Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (500ml) with Rubber Cap",
  "Glucose 5% Infusion - Bottle (100ml) with Rubber Cap -", "Glucose 5% Infusion - Bottle (250ml) with Rubber Cap", "Glucose 5% Infusion - Bottle (250ml)(ROM)", "Glucose 5% Infusion - Bottle (500ml) (ROM) - [", "Glucose 5% Infusion - Bottle (500ml) with Rubber Cap -", "Glutaraldehyde 2.3 % - UCCMADEX", "Glutamine (L-Alanyl L-Glutamine) Infusion - Bottle (100ml)",
  "Glycerine Adult Suppository", "Glycerine Pediatric Suppository", "Glyceryl Trinitrate 10mg - Patch -", "Glyceryl Trinitrate 1mg/ml - Vial", "Glyceryl Trinitrate 2.5mg - Capsule", "Glyceryl Trinitrate 50 mg( Nitroglycerin 50 mg )", "Glyceryl Trinitrate 5mg - Capsule -", "Glyceryl Trinitrate 5mg - Patch - [", "Glycine 1.5% Irrigation - Bag (1L)",
  "Glycine 1.5% Irrigation - Bag (3L) -", "Golimumab 50 mg", "Goserolin Acetate 10.8 mg - prefilled syringe", "Goserolin Acetate 3.6 mg - prefilled syringe", "Gramicidin + Neomycin + Nystatin + Triamcinolone Cream", "GRANISETRON 3 mg ampoule", "Granisetron 1mg/1ml - Ampoule (1ml)", "Granisetron 1mg/5ml - Bottle", "Granisetron 1 mg TABLET",
  "Granisetron 2mg - Tablet", "GRISEOFULVIN 125MG - Cap/Tab", "Guselkumab 100 mg/ml - syringe", "H2O2 30% 1 litre", "HALOPERIDOL 1.5MG - Tablet -", "HALOPERIDOL 50MG/ML - Ampoule", "HALOPERIDOL 5MG - Tablet", "HALOPERIDOL 5MG/ML - Ampoule", "Halphabarol 0.4mg - Tablet", "Halothane Inhaler Solution (100ml )", "Halothane Inhaler Solution (250ml) -",
  "Heparin Calcium 5000 IU - Ampoule", "Heparin Sodium 5000 IU - Ampoule (1ml) -", "Hepatitis B Immunoglobulin 180 IU Vial 1 ml", "Hepatitis B Immunoglobulin 540 IU Vial 3 ml", "Hexamine + Piperazine - Effervescent Granules Bottle", "Hirudin 420 I.U.", "Human Albumin 20% I.V infusion", "Human Chorionic Gonadotropin 5000 I.U.",
  "Human Menopausal Gonadotropin (FSH+LH) 150 I.U", "Human Menopausal Gonadotropin (FSH+LH) 75 I.U", "Hyaluronate - Blink tears (sodium hyaluronate )- Eye drops", "Hydralazine 25mg - Film Coated Tablet", "Hydralazine 50mg - Film Coated Tablet", "Hydrochlorothiazide 25 mg -Tab/Cap-", "Hydrocortisone 1 % + Oxytetracycline 3 % Ointment",
  "Hydrocortisone 1 % Cream", "Hydrocortisone 1 % Ointment", "Hydrocortisone Micronized 10 mg - Tablet", "Hydrocortisone sodium succinate 100 mg Vial -", "HYDROGEN PEROXIDE 10 VOL-100 ML - Solution", "HYDROGEN PEROXIDE 20 VOL-100 ML - Solution - Bottle", "Hydroxy Propyl methyl celllulose 0.3% - Eye Drop", "HYDROXYCHLOROQUINE 200MG - Cap/Tab",
  "Hydroxyethyl Corn-Starch 6% Infusion - Bottle (500ml) -", "Hydroxyurea 500 mg", "Hyoscine + Analgesic - Ampoule -", "Hyoscine 10mg - Tablet -", "Hyoscine 20mg/ml - Ampoule (Solution for I.M. or Slow I.V.)", "Hypertonic sea water 70% - NaCl 2.3% + Eucalyptus + Spearmint + thyme - Spray", "Ibandronic Acid 150 mg - Film Coated Tablet",
  "Ibrutinib 140 mg capsule", "Ibuprofen 100mg/5ml - Suspension/Syrup - Bottle (100ml) -", "Ibuprofen 100 mg Suppository", "Ibuprofen 200mg - Cap/Tab -", "Ibuprofen 300 mg supp", "Ibuprofen 400mg cap/tab", "Ibuprofen 500 mg Suppository", "Ibuprofen 600 mg cap / tab", "Ibuprofen 600mg sachet", "Ibuprofen up to 50 mg / ml Oral drop",
  "Ichthammol + Hamamelis + Potassium Iodide - supp", "Idursulfase Beta Vial - Huntrase 6mg", "Ifosfamide 1 gm vial", "Ifosfamide 2 gm vial", "imatinib 100 mg -  Tablet", "imatinib 400 mg  Tablet", "IMIPENEM 500MG + CILASTATIN 500MG - Vial", "IMIPRAMINE 25MG", "Imiglucerase 400 unit -  Vial", "Immunoglobulin IV IG 2.5 gm - Vial",
  "Immunoglobulin IV IG 5 gm", "Indapamide 1.5mg - Film Coated Tablet", "Indapamide 2.5mg tab", "Indacaterol 150 mcg capsule", "Indacaterol 300 mcg capsule", "Indomethacin 100 mg tab/ cap", "Indomethacin 100mg - Suppository", "Indomethacin 25 mg cap/tab", "Indomethacin 50mg ampoule", "Influvac Tetra Vaccine 1 Prefilled Syringe-",
  "Infliximab 100 mg vial", "INSULiN ASPART 100 I.U./ml+insulin aspart protamine-novomix", "Insulin  intermediate  acting .100IU/ml - Cartridge 3ml", "Insulin  short acting Hydrous 100IU/ml - Cartridge 3ml -", "Insulin  short acting  Hydrous Vial -", "Insulin Glaringe 100 IU/ml + Lixisenatide 33 mcg - Pen", "Insulin Glaringe 100 IU/ml + Lixisenatide 50 mcg - Pen",
  "Insulin Glulisine 100 IU/mg", "Insulin Glulisine human - Apidra Cartridge 100 Unit/ml", "Insulin Intermediate acting isophane - vial", "Insulin lispro 100 IU/ml cartridge", "Insulin lispro 200 IU/ml pen", "Insulin Mixed -  30/70 vial", "Insulin Mixed 100IU/ml 70/30 - Cartridge 3ml", "insulin aspart 50 mg+INSULIN ASPART protamine 100 I.U./ml ,novomix 50",
  "insulin lispro 25 % solution and 75% insulin lispro protamine suspension( mix ) cartridge", "insulin lispro25 % solution and 75% insulin lispro protamine suspension ( mix) pen", "insulin lispro 50 % solution and 50% insulin lispro protamine suspension( mix) pen", "Intelence 100MG", "Interferon Beta-1A 132mcg/1.5ml - Prefilled Syringe (S.C.)",
  "Interferon Beta-1A 30mcg/0.5ml", "Interferon Beta-1A 44mcg/0.5ml - Prefilled Syringe (S.C.)", "Interferon beta-1b- BETAFERON 250 mcg/ml (S.C) Vial + Solvent", "Iobitridol 300mg/ml - Vial (50ml)", "Iobitridol 350mg/ml - Vial (50ml)", "Iopamidol 300/ml vial 50 ml", "Iopamidol 370/ml vial 50 ml", "ipratropium 0.02 mg + fenoterol 0.05 mg",
  "Ipratropium 0.02 mg + Salbutamol 0.12 mg", "Ipratropium bromide 0.5 mg + salbutamol 2.5 mg", "Ipratropium bromide 250 mcg -", "Ipratropium bromide 500 mcg", "Irbesartan 150mg - Tablet", "Irbesartan 150mg + Hydroclorothiazide 12.5mg - Tablet", "Irbesartan 300mg - Tablet", "Irbesartan 300mg + Hydroclorothiazide 12.5mg tablet",
  "Irbesartan 300mg + Hydrochlorothiazide 25mg - Tablet", "Irinotecan 100 mg vial", "Irinotecan 300mg - vial (5ml)lv/ lm", "Irinotecan 40 mg", "Iron I.M 100mg", "iron syrup ( 8 - 10 ) mg / ml", "Iron Oral drop", "Iron sucrose 100mg/5ml - Ampoule", "Isavuconazole 200 mg - Vial", "Isentress 25 mg", "Isoconazole 1% + Diflucortolone 0.1%",
  "Isoconazole 600 mg VAG. OVULE", "Isoflurane - Inhaler with Vaporizer bottle", "ISONIAZID 100MG", "Isoniazid 300 mg", "ISOPROPYL ALCOHOL 70% 1LIT Bottle 1 L", "ISOPROPYL ALCOHOL 70% 4LIT Bottle 4 L", "ISOPROPYL ALCOHOL 90% 1LIT Bottle 1 L", "ISOPROPYL ALCOHOL 90% 4LIT Bottle 4 L", "Isosorbide Dinitrate 10 mg",
  "Isosorbide Dinitrate 20mg - Capsule", "Isosorbide Dinitrate 40mg", "Isosorbide Dinitrate 5 mg - Tablet", "Isosorbide Mononitrate 100mg", "Isosorbide Mononitrate 20mg", "Isosorbide Mononitrate 25mg - Capsule", "Isosorbide Mononitrate 40 mg tablet", "Isosorbide Mononitrate 50mg - Capsule", "Isotretinoin 10 mg - Capsule",
  "Isotretinoin 20 mg - Capsule", "Isotretinoin 40 mg - Capsule", "ITRACONAZOLE 100MG", "Itopride Hydrochloride 50 mg tablet", "Ivabradine 5mg - Film Coated Tablet", "Ivabradine 7.5mg - Film Coated Tablet", "IVERMECTIN 6MG - Cap/Tab", "Ivermectin 1 % Lotion", "Ixazomib 2.3mg capsule", "Ixazomib 3mg capsule", "Ixazomib 4mg capsule",
  "KANUMA 2 mg/ml concentrate for solution for infusion - (sebelipase alfa)", "Kaolin 1 g + Pectin 20 mg / 5 ml Suspension", "Ketamine 500mg/10ml vial", "ketoconazole 2% (shampoo) bottle", "KETOCONAZOLE 2% - Cream - Tube", "Ketoprofen 100mg cap/tab", "ketoprofen 100 mg suspension", "Ketoprofen 100mg supp", "Ketoprofen 100mg/2ml ampoule",
  "Ketoprofen 150mg - Cap/Tab", "Ketoprofen 2.5 % gel", "Ketoprofen 200mg cap/tab", "Ketoprofen 25mgcap/tab", "Ketoprofen 50mg cap/tab", "Ketoprofen 75mg - Cap/Tab", "Ketorolac 10mg - Cap/Tab", "Ketorolac 15mg - Ampoule", "Ketorolac 30mg - Ampoule", "Ketotifen 0.025 % Eye Drops", "KETOTIFEN 0.25 mg/ml (e.d) ampoule 0.4 ml",
  "Ketotifen 1 mg / 5 ml Syrup", "Ketotifen 1 mg tablet", "Khellin 20 mg + Cymbopogon Proximus sachet", "L- Asparaginase 10000 I.U", "L-asparaginase 800 VL", "L-Carnitine 1 gm / 5 ml", "L-Carnitine 1 gm + Zinc gluconate 50 mg film Coated Tablet", "L - Carnitine 300 mg/ml ( syrup) Bottle", "L-Carnitine 30 %", "L - CARNITINE PLUS Film Coated Tablet",
  "L-Carnitine up to 350 mg", "L-Ornithine L-Aspartate 5g/10ml", "L- Ornithine -L- Aspartate sachet", "L-ORNITHINE-L-ASPARTATE 5 gm vial", "L-Thyroxine Injectable Ampoules - Levothyroxine sodium", "Labetalol 100mg", "Labetalol 200mg", "Lacosamide 100mg tablet", "Lacosamide 10mg/ml syrup", "LACOSAMIDE 50MG - Tablet",
  "Lactase enzyme - Drops", "LACTOBACILLUS ACIDOPHILUS FOR ADULT CAPSULE", "LACTOBACILLUS ACIDOPHILUS FOR CHILDREN - Sachet", "Lactoferrin 100mg Sachet - Sachet", "Lactulose - Syrup 200ml", "Lactulose (65%-67%) - Sachet", "Lactulose 66% - syrup Bottle 120 ml", "LAMIVUDINE 100MG - Cap/Tab", "LAMIVUDINE 150MG cap /tab",
  "LAMIVUDINE 150MG + ZIDOVUDINE 300MG cap / tab", "LAMIVUDINE zadovidine 30 mg 60mg", "Lamotrigine 100 mg tablet", "Lamotrigine 200MG - Tablet", "Lamotrigine 25 mg tablet", "Lamotrigine 50mg tablet", "Lansoprazole 15 mg Capsule", "lansoprazole 30 mg capsule", "LANTUS SOLOSTAR 100IU/ml PEN Prefilled Pen", "Lantus Cartridge 100 Unit/ml",
  "Lapatinib 250 mg", "Latanoprost 50 mcg + Timolol 5 mg - EYE DROPS", "Latanoprost 50 mcg/ ml ( e.d) bottle", "Leflunomide 10 mg", "Leflunomide 20mg With Providing The Loading Dose", "Lenalidomide 25 mg capsule", "Lenalidomide 5 mg capsule", "Lercanidipine 10mg - Tablet", "LETROZOLE 2.500 mg tablet", "Levamisole HCl 40 mg",
  "Levemir 100IU/ml Flexpen", "LEVETIRACETAM 1000MG - Film Coated Tablet", "LEVETIRACETAM 100MG/ML - Syrup", "Levetricetam 100mg/ml syrup", "LEVETIRACETAM 250MG - Film Coated Tablet", "Levetiracetam 500mg/5ml Syrup", "LEVETIRACETAM 500MG - Film Coated Tablet", "LEVETIRACETAM 500MG XR - Film Coated Tablet", "LEVETIRACETAM 500MG ampoule",
  "levocetirizine 5 mg / 10ml syrup", "levocetirizine 5 mg /ml drops", "LEVOFLOXACIN 500MG - Cap/Tab", "LEVOFLOXACIN 500MG - Vial + Solvent", "Levofloxacin 750 mg tab/cap", "Levofloxacin 750MG -vial", "Levonorgestrel 0.3mcg - Tablet", "Levonorgestrel 0.75mg - Sublingual Tablet", "Levothyroxine 100 mcg tablet", "Levothyroxine 25 mcg tablet",
  "Levothyroxine 50 mcg tab", "Lidocaine 1% - Ampoule or Vial (5ml) - Lidocaine Hydrochloride 50mg/5ml", "Lidocaine 10% - Spray", "Lidocaine 2% (2ml)", "Lidocaine 2% (50ml) - Ampoule or Vial", "Lidocaine 40mg /ml spray", "Lidocaine + Aminacrine Oral Gel", "Lidocaine Hydrochloride 20 mg/gm ; calcium dobesilate monohydrate 40 mg/gm ; Dexamethasone Acetate 0.25 mg/gm ; (Rectal Oint.) Tube 30 gm",
  "Lignocaine 5 % (cream) Tube", "LINEZOLID 100MG/5ML suspension", "LINEZOLID 2MG/ML - Vial (100ml)", "LINEZOLID 2MG/ML - Vial (300ml)", "LINEZOLID 600MG - Cap/Tab -", "Lipid Infusion (for Adult) - Bottle (250ml) - [SMOFlipid 20%]", "Lipid Infusion (for Adult) - Bottle (500ml) - [SMOFlipid 20%]", "Lipid Infusion (for neonates)",
  "Lisinopril 10mg tablet", "lisinopril 20 mg - Tablet", "Lisinopril 20mg + Hydrochlorothiazide 12.5mg tablet", "Lisinopril 5mg tablet", "LITHIUM CARBONATE 400MG tablet", "Loperamide 2mg - Cap/Tab -", "lopinavir/ritonavir", "Loratadine 10 mg tablet", "Loratadine 5 mg / 5 ml syrup", "Lornoxicam 4mg - Cap/Tab", "Lornoxicam 8mg - Cap/Tab",
  "Losartan 100mg + Hydrochlorothiazide 12.5mg - Film Coated Tablet", "Losartan 100mg + Hydrochlorothiazide 25mg tablet", "Losartan 100mg - Tablet", "Losartan 50mg + Hydrochlorothiazide 12.5mg tablet", "Losartan 50mg tablet", "Low-Molecular Weight Heparin For Prophylaxis For Daily Dose-innohip", "Low-Molecular Weight Heparin For Treatment Of Patients Over 80 KG For Daily Dose-innohip",
  "Lubiprostone 24mcg -cap", "Lubiprostone 8mcg cap", "Lung Surfactant For Neonatal Respiratory Distress Syndrome High Dose", "Lung Surfactant For Neonatal Respiratory Distress Syndrome Low Dose", "LYOPHILIZED BACTERIAL LYSATE 3.5MG - [Broncho-Vaxom Children] CAP/TAB", "LYOPHILIZED BACTERIAL LYSATE 7MG - [Broncho-Vaxom Adults] CAP/TAB",
  "LYOPHILIZED BACTERIAL LYSATE [Broncho-Vaxom Children] SACHET", "Macitentan 10mg - Film Coated Tablet", "Magaldrate - Tablet -", "Magaldrate - Suspension", "Magnesium Citrate - Effervescent Granules Sachet", "Magnesium Gluconate + Magnesium Oxide - Tablet", "Magnesium oxide + calcium carbonate + zinc sulphate- Tablet -",
  "Magnesium SULPHATE 10 % Ampoule 25 ml", "Magnesium Sulfate 10% - Ampoule 10 ML", "Magnesium Sulphate 500 g", "Magnesium Sulphate sachet", "Malathion 0.5 % lotion", "Mannitol 10% Infusion - Bottle (500ml)", "Mannitol 10% Infusion - Bottle (500ml) (ROM)", "Mannitol 10% Infusion - Bottle (500ml) with Rubber Cap",
  "Mannitol 20% Infusion - Bottle (500ml)", "Mannitol 20% Infusion - Bottle (500ml) (ROM)", "Mannitol 20% Infusion - Bottle (500ml) with Rubber Cap", "MEBENDAZOLE 100MG", "MEBENDAZOLE 100MG/5ML", "MEBENDAZOLE 500MG", "Mebeverine + Chlorodiazepoxide tablet", "Mebeverine + Sulpiride tablet", "Mebeverine 100mg tablet", "Mebeverine 135mg",
  "Mebeverine 200mg cap / tab", "mecasermin 40 mg/ 4ml", "MECLOFENOXATE 250MG Tablet", "MECLOFENOXATE 500MG Tablet", "MECLOFENOXATE 500MG vial", "Meclizine 50mg Tablet", "Medicated Tulle With Antibiotics 10 cm * 30 cm", "Medicated Tulle With Antibiotics 10*10 cm", "Medroxyprogesterone acetate 150mg/ml vial", "Meglumine Ioxitalamate + Sodium Ioxitalamate - Vial (50ml)",
  "Meloxicam 15mg ampule", "Meloxicam 15mg supp.", "Meloxicam 15mg Tablet /Capsule", "Meloxicam 7.5mg Tablet /Capsule", "MELOXICAM  5 mg Ampoule", "Melphalan 2mg tablet", "Memantine 10 mg tablet", "Memantine 20 mg - tablet", "MEMANTINE - Oral Drops", "Memantine 2mg /ml- Bottle 100 ml", "Memantine 5 mg - tablet", "Menaquinon 7 (MK-7) - Tablet",
  "menthol+ camphor + ketoprofen (gel) tube", "Mepadrebal -Mepivacaine Hydrochloride 36 mg/1.8 ml 2%- Dental anesthesia", "Mepivacaine 2 % + Levo Nordefrine", "Mepivacaine Hydrochloride 3%", "Mercaptopurine 50mg Tablet", "MEROPENEM 1000MG - Vial", "MEROPENEM 500MG - Vial", "Mesalazine 1g - Rectal Suppository", "Mesalazine 1g - Sachet",
  "Mesalazine 1g/100ml", "Mesalazine 2g sachet", "mesalazine 400 mg - Capsule", "Mesalazine 500mg - Tablet /Capsule", "MESNA 400 mg vial", "Mestrolone 25 mg Tablet", "Metformin 1000 mg  - Tablet/capsule", "Metformin 1000 mg + Glibenclamide 5 mg - Tablet/capsule", "Metformin 1000 mg + Vildagliptin 50 mg - tablet",
  "Metformin 500 mg  - Tablet/capsule", "Metformin 500 mg + Glibenclamide 5 mg film Coated Tablet", "Metformin 850 mg + Vildagliptin 50 mg - tablet", "Metformin 850 mg tablet/capsule", "Metformin X R 1000 mg film Coated Tablet", "METFORMIN 400 mg + GLIBENCLAMIDE 2.5 mg tablet", "METFORMIN 800 mg + GLIBENCLAMIDE 5 mg tablet",
  "Methocarbamol + Analgesic - Cap/Tab", "Methotrexate 2.5 mg tablet", "Methotrexate 5 gm Vial (Ebewe)", "Methotrexate 50 mg / 2ml vial", "Methotrexate 500 mg vial", "Methoxsalen 10 mg tablet", "Methoxy polyethylene glycol-epoetin beta 50 mcg prefilled syringe", "Methyldopa 250mg Tablet", "METHYLPHENIDATE 18MG - Extended Release Tablet",
  "METHYLPHENIDATE 36MG - Extended Release Tablet", "Methylprednisolone 1000 mg - Vial", "METHYLPREDNISOLONE 40 mg/ml - Vial", "Methylprednisolone 500 mg - Vial", "Methylergometrine 0.125 mg tablet", "Methylergometrine 200 mcg / 1 ml Ampoule", "Metoclopramide 10 mg tablet", "Metoclopramide 10mg/2ml Ampoule",
  "Metolazone 5mg - Tablet", "Metoprolol 100mg - Tablet", "Metoprolol 200mg - Tablet", "Metoprolol 25mg - Tablet", "Metoprolol 50mg - Tablet", "Metoprolol 5mg - Ampoule", "METRONIDAZOLE 125MG/5ML - Syrup", "METRONIDAZOLE 1G supp.", "METRONIDAZOLE 200MG/5ML - Syrup", "METRONIDAZOLE 250 MG - tablet /capsule", "METRONIDAZOLE 500 MG - tablet /capsule",
  "METRONIDAZOLE 500MG - Solution for I.V. infusion", "Metronidazole 500 mg Vaginal Suppository", "MICAFUNGIN 50MG - Vial + Solvent", "Miconazole 2 % Cream", "Miconazole 2 %Oral Gel", "Miconazole 2 % vaginal Cream", "Miconazole 2 % Powder", "Miconazole 200 mg vaginal tablet", "MICONAZOLE NITRATE 2% spray", "MICONAZOLE NITRATE 400 mg vaginal ovule",
  "MIDAZOLAM 15MG/3ML  Ampoule", "MIDAZOLAM 5MG/ML - Ampoule", "Midodrine 1 % oral drops", "Midodrine 2.5mg - Tablet", "Miglustat 100mg Tablet", "milga (vit B1+B6+B12)", "MILNACIPRAN 25MG - Film Coated Tablet", "MILNACIPRAN 50MG - Film Coated Tablet", "Milrinone 1mg/ml - Ampoule", "Mineral Oil 10% + Dexa Panthenol 5% + Thyme Oil 0.5% + Tocopherol 0.1% (cream) tube",
  "Minoxidil 5% - Spray 60 ml", "Mirabegron 50mg - Tablet /Capsule", "Mirena IUD - intra Utrine Delivery system", "MIRTAZAPINE 30MG - Tablet", "Misoprostol 200 mcg - Tablet", "Mitoxantrone 2 mg / ml vial", "Mixture of Standardized Natural Extracts 120 ml - Sugar free - Syrup", "Mixture of Standardized Natural Extracts 120 ml - Syrup",
  "Molnupiravir 200mg - Cap/Tab", "Mometasone 0.1 % Cream", "Mometasone 0.1 % Ointment", "mometasone furoate monohydrate 50 mcg/dose (nasal spray)", "Montelukast 10 mg tablet/capsule", "Montelukast 4mg Sachet", "Montelukast 5 mg Chewable Tablet", "Montelukast Sodium 4 mg - tablet", "Morphine 30mg Tablet /Capsule",
  "Morphine Sulphate 10mg/ml Ampoule", "Morphine Sulphate 20mg/ml Ampoule", "Mosapride 5mg - Tablet", "Mouth Wash Containing Chlorhexidine", "MOXIFLOXACIN 400MG Tablet /Capsule", "Moxifloxacin 400 mg vial", "Moxifloxacin 5 mg / ml Eye drops", "MOXIFLOXACIN 500MG Tablet /Capsule", "Mucolytic Syrup Containing Ambroxol", "Multivitamins & Minerals Capsule",
  "Mycophenolate Mofetil 250 mg Tablet - for transplantation", "Mycophenolate Mofetil 500mg Tablet /Capsule- for autoimmune", "Mycophenolate Mofetil 500mg Tablet /Capsule-for transplantation", "Mycophenolic acid 180 mg Tablet /Capsule", "Mycophenolic acid 360 mg Tablet /Capsule", "Na Hyaluronate 1% vial", "Naftidrofuryl 200mg - Capsule",
  "Nalbuphine 20mg/ml ampoule/vial", "Naloxone 0.4mg/ml Ampoule", "Naltrexone 50 mg Tablet", "Naphazoline + Chlorpheniramine Eye Drops", "Nasal Drop Containing Oxymetazoline For Adults", "Nasal Drop Containing Oxymetazoline For Infants", "Nasal Gel Containing Decongestant", "Nasal Spray Containing Decongestant", "Nasogastric tube", "Natalizumab 20mg/ml - Vial (I.V.)",
  "Nebivolol 2.5mg - Tablet", "Nebivolol 5 mg -Tablet", "Nebivolol 5mg + Hydrochlorothiazide 12.5mg - Tablet", "Nebivolol 5mg + Hydrochlorothiazide 25mg Tablet", "Neomycin + Bacitracin Powder", "Neostigmine 0.5mg/1ml ampule", "Neostigmine 2.5 mg / 1 ml vial", "Neulastim 6 mg / 0.6 ml - Pegfilgrastim 6 mg / 0.6 ml", "Neutral vaginal douche sachet",
  "NEVIRAPINE + lamivudine- syrup", "NEVIRAPINE 200MG - Tablet /Capsule", "NEVIRAPINE 50MG - syrup", "Niclosamide 500 mg - tablet", "Nicorandil 10mg - Tablet", "Nicorandil 20mg - Tablet", "Nicotinamide+PANTHENOL+VITAMIN B1+VITAMIN B2+VITAMIN B6 - amp.", "Nifedipine 10 mg - tablet /capsule", "Nifedipine 20 mg Retard capsule", "NIFUROXAZIDE 200MG/5ML - Suspension",
  "Nifuroxazide 200 mg - tablet /capsule", "Nilotinib 150 mg tablet", "Nilotinib 200 mg - tablet", "NIMODIPINE 0.2MG/ML - Vial", "Nintedanib 100 mg capsule", "Nintedanib 150 mg capsule", "Nitisinone 10mg - Tablet", "Nitazoxanide 100mg/5ml - Susp", "Nitazoxanide 500mg - Tablet", "NITROFURANTOIN 100MG Tablet /Capsule", "NITROFURANTOIN 50MG Tablet /Capsule",
  "Nivolumab 100mg/10 ml vial", "Nivolumab 40mg/10ml vial", "Nonoxynol-9 100 mg in a polyethylene glycol base - Vaginal Suppository", "Noradrenaline 8mg/4ml Ampoule /vial", "Norethisterone 5 mg - tablet", "Nortriptyline 10 mg + Fluphenazine 0.5 mg Tablet", "Novorapid 100IU/ml Penfil 3ml - (INSULIN ASPART 100 I.U. )",
  "NYSTATIN 100,000 I.U./ML - Oral Drops", "Obidoxime Chloride 250mg", "Obinutuzumab 1000mg vial", "Ocrelizumab 300mg/10ml vial", "Octreotide 0.1mg/ml - amp.", "Octreotide 30 mg Vial+ Prefilled syrine of solvent+ 2 sterile injection needles", "OFLOXACIN 200MG - Cap/Tab", "Ofloxacin 0.3 % Eye Drops", "OFLOXACIN 400MG- Cap/Tab",
  "Olaparib 100mg - Tablet", "Olaparib 150mg - Tablet", "OLANZAPINE 10MG - Tablet /Cap", "OLANZAPINE 12MG + FLUOXETINE 25MG -Capsule", "OLANZAPINE 12MG + FLUOXETINE 50MG - Capsule", "OLANZAPINE 5 MG tablet/capsule", "OLANZAPINE 6MG + FLUOXETINE 25MG - Capsule", "OLANZAPINE 6MG + FLUOXETINE 50MG - Capsule", "OLANZAPINE 7.5MG - Tablet",
  "Olimel N9E (1200 Kcal/1000ml)", "olmesartan 20 mg - Tablet", "Olmesartan 20mg + Hydrochlorothiazide 12.5mg - Tablet", "olmesartan 40 mg Tablet", "Olmesartan 40mg + Hydrochlorothiazide 12.5mg - Tablet", "olopatadine hydrochloride 0.1% Eye Drops", "Omalizumab 150 mg vial", "Omega 3 Plus Capsule", "Omega 3. Vit D Vit A Vit C tablet",
  "Omeprazole 10mg - Capsule", "Omeprazole 20 mg - Tablet /Capsule", "Omeprazole 20mg + Sodium Bicarbonate 1100mg - Capsule", "Omeprazole 20mg + Sodium Bicarbonate 1680mg - Sachet - Granules for Oral Suspension", "Omeprazole 40 mg - Tablet /Capsule", "Omeprazole 40mg + Sodium Bicarbonate 1100mg Capsule /Tablet", "Omeprazole 40mg + Sodium Bicarbonate 1680mg",
  "Omeprazole 40mg vial", "Ondansetron 4mg  Orodisperdible film", "Ondansetron 4mg  Tablet /Capsule", "Ondansetron 4mg/2ml - Ampoule", "Ondansetron 8mg - Orodisperdible film", "Ondansetron 8mg - Tablet /Capsule", "Ondansetron 8mg/4ml - Ampoule", "Oral Aminoacids For treatment Dialysis Patients", "ORNIDAZOLE 500MG Tablet /Capsule",
  "Oseltamivir 12 mg / ml syrub - 15 g - ( Bottle 30 ml )", "Oseltamivir 12 mg / ml syrub - 30 g - ( Bottle 60 ml )", "Oseltamivir 75 mg - Capsule", "Osimertinib 80mg - Tablet", "Ossofortin 0.25 mg Film Coated Tablet - Ergocalciferol 0.25 mg Eq.to 10000 IU -", "Otilonium 40mg - Tablet", "Oxaliplatin 100 mg vial", "Oxaliplatin 50 mg vial",
  "OXCARBAZEPINE 150mg tablet", "OXCARBAZEPINE 300MG - Film Coated Tablet", "OXCARBAZEPINE 600MG - Film Coated Tablet", "OXCARBAZEPINE 60MG/ML - Suspension - Bottle (100ml)", "OXYBUTYNIN 10mg - Tablet", "Oxybutynin 5 mg / 5 ml syrup", "Oxybutynin 5mg - Tablet", "OXYCODONE 10MG - Tablet", "OXYCODONE 5MG - Tablet", "Oxytocin 10 units Ampoule",
  "Oxytocin 5 Units Ampoule", "Paclitaxel 100 mg Vial", "Paclitaxel 150 mg / 25 ml vial", "Paclitaxel 30 mg Vial", "Paclitaxel 300 mg vial", "Palbociclib 100mg + Exemestane 25mg Capsule", "Palbociclib 100mg capsule", "Palbociclib 125mg + Exemestane 25mg Capsule /Tablet", "Palbociclib 125mg capsule", "Palbociclib 75mg + Exemestane 25mg Capsule / Tablet",
  "Palbociclib 75mg capsule", "PALIPERIDONE 100MG - Prefilled Syringe", "PALIPERIDONE 150MG - Prefilled Syringe", "PALIPERIDONE 350MG - Prefilled Syringe", "PALIPERIDONE 3MG - tablet", "PALIPERIDONE 525MG - Prefilled Syringe", "PALIPERIDONE 6MG - Tablet", "PALIPERIDONE 75MG - Prefilled Syringe", "Palonosetron 0.05mg/ml vial", "Palonosetron 0.25 mg/5ml vial",
  "Pamidronate 90 mg vial", "Pancuronium 4mg Ampoule", "Panitumumab 100 mg - Vial", "Panitumumab 400 mg - Vial", "Pantoprazole 20mg - Tablet/Capsule", "Pantoprazole 40 mg - Capsule/Tablet", "Pantoprazole 40 mg + EDTA - Injection", "Pantoprazole 40mg - Vial", "PAPAIN+PAPSIN+SANZYME3500", "Paracetamol  + chlorzoxazone",
  "Paracetamol + Chlorpheniramine", "Paracetamol + Pseudoephedrine +Chlorphenramin -syrup", "Paracetamol + Pseudoephedrine +Dextromethorphan + Diphenhydramine Tablet", "Paracetamol 1 gm/100ml - Vial", "Paracetamol 100mg - Oral Drops", "Paracetamol 120mg/5ml - syrup", "Paracetamol 125mg supp.", "Paracetamol 1G Tablet /Capsule",
  "Paracetamol 500mg - Cap/Tab", "Paracetamol 500mg + Caffeine - Cap/Tab", "Paracetamol 500mg + Diphenhydramine HCl 12.6mg - Cap/Tab", "Parrafin oil 1 litre", "PAROXETINE 12.5MG - Film Coated Tablet", "PAROXETINE 20MG - Film Coated Tablet", "Paroxetine 25mg - Film Coated Tablet", "PAROXETINE 37.5MG - Film Coated Tablet", "Patant Blue V Syringe 2.5% (2ML) Ampoule",
  "Pazopanib 200 mg tablet-votrient", "Pazopanib 400 mg -votrient Cap/Tab", "PEFLOXACIN 400MG - Cap/Tab", "PEG - أنبوبة من البولي يوريثان للتغذية المعدية على المدى الطويل", "Pegylated interferon Alfa-2 160mcg-reiferon vial", "Pegylated liposomal doxorubicin 20 mg-caelyx vial", "Pembrolizumab 100 mg/4 ml vial", "Pemetrexed 500 mg vial",
  "Penicillamine 250mg - Capsule", "Penicillamine 300 mg - Tablet", "Pentostam (Sodium Stibogluconate ) BP 100 mg/ml vial", "Pentoxifylline 100mg/5ml - Ampoule", "Pentoxifylline 400 mg - tablet", "Perindopril 10mg - Film Coated Tablet", "Perindopril 10mg + Indapamide 2.5mg - Film Coated Tablet", "Perindopril 5mg - Film Coated Tablet",
  "Perindopril 5mg + Indapamide 1.25mg - Film Coated Tablet", "Periolimel N4E (1050 Kcal/1500ml)", "Pertuzumab 420 mg + Trastuzumab 600mg S.C vial", "Pethidine 100mg/2ml Ampoule", "Pethidine 50mg/ml Ampoule", "PHENOXYMETHYLPENICILLIN 1,000,000 I.U. Tablet /Capsule", "PHENOXYMETHYLPENICILLIN 1,500,000 I.U. Tablet /Capsule",
  "Phentolamine 10mg vial", "Phenylephrine 10mg/ml - Ampoule/Vial", "Phenylephrine HCL 2.5 % Eye Drops", "Pheniramine 45.5 mg /2 ml Ampoule", "PHENYTOIN 100MG - Capsule", "PHENYTOIN 30MG/5ML", "PHENYTOIN 40 mg / 150 ml", "PHENYTOIN 50MG Capsule", "PHENYTOIN 50MG/ML Ampoule", "Phospholipid fraction from bovine lung 54mg/1.2ml Vial - Natural Surfactant - Alveofact vial",
  "Phytomenadione (Vitamin K) 10mg Ampoule", "Phytomenadione (Vitamin K1) 10mg chewable tablet", "Pilocarpine Nitrate 2 % Eye Drops", "PIMECROLIMUS 10 mg - Cream", "PIMOZIDE 4MG Tablet", "Pinaverium 100mg - Tablet", "Pinaverium 50mg - Tablet", "Pinene + Borneol + Camphene + Menthol + Cineole + Menthone - Capsule -",
  "Pioglitazone 15 mg + Metformin HCl 500 mg Tablet", "Pioglitazone 15 mg + Metformin HCl 850 mg Tablet", "Pioglitazone 15mg - Tablet", "Pioglitazone 30mg + Glimepiride 4mg Tablet", "Pioglitazone 30mg - Tablet", "pioglitazone 45 mg tablet", "Pipazethate 10 mg supp.", "Pipazethate 20 mg Tablet", "Piperacillin 4 gm + Tazobactam 500 mg -Vial 50 ml",
  "Piperazine + Colchicine - Effervescent Granules Sachet", "PIRACETAM 1GM/5ML Ampoule", "PIRACETAM 200MG/ML", "PIRACETAM 400MG Capsule", "PIRACETAM 800MG Tablet", "PIRIBEDIL 20 mg tablet", "PIRIBEDIL 50MG Tablet", "Pirfenidone 267 mg - Capsule", "Piroxicam 0.5 % gel", "Piroxicam 10mg Tablet", "Piroxicam 20mg Ampoule",
  "Piroxicam 20mg - Cap/Tab", "Piroxicam 20mg Supp.", "Pitavastatin 1mg - Tablet", "Pitavastatin 2mg - Tablet", "Pitavastatin 4mg - Tablet", "Polidocanol 10mg/ 2ml - Ampoule", "Polidocanol 20mg/ 2ml - Ampoule", "Poly B bion Vitamin B Containing At Least (B1, B6,B12) Ampoule", "Polyethylene Glycol + Propylene Glycol Eye Drops 10 ml", "Polyethylene glycol + propylene glycol Eye Gel 10ml",
  "Polyvidone 5% (e.d) bottle 15 ml- orchatears", "Ponesimod 20mg + Titration Pack Ponesimod (2,3,4,5,6,7,8,9,10)mg FOC for each new Patient when ordering 3 Boxes] - Film Coated Tablet - [Ponvory]", "poractant alfa 1.5 ml (80 mg/ml)(Intratracheal Suspension)", "poractant alfa 3 ml (Intratracheal Suspension)", "POSACONAZOLE 40MG/ML - Suspension - Bottle", "Posaconazol 300mg - Vial",
  "Potassium Chloride 15% Ampoule", "Potassium Chloride 600 mg Tablet", "Potassium Chloride Syrup", "Potassium Iodide 65mg tablet", "Potassium Sodium Hydrogen Citrate - Effervescent Granules Jar 280gm", "potassium permenganate 500 gm -", "Povidone Iodine 10 % Oint - Tube 60 gm", "Povidone Iodine 10 % Solution Bottle 1 L With Dispenser",
  "Povidone Iodine 10% - (4L)", "Povidone-Iodine 10% - Bottle 120ml", "Povidone Iodine 7.5 % Shampoo (200ml)", "Povidone Iodine 7.5 % Solution (1L with dispenser)", "Povidone Iodine 7.5% Skin Cleanser - 200ml", "Povidone Iodine mouthwash", "Pralidoxime Chloride 1gm", "Pramipexole 0.18MG - Tablet", "Pramipexole 0.5 mg Tablet", "Pramipexole 0.7MG - Tablet",
  "Praziquantel 600 mg tablet", "Prednisolone 1% Eye Drops -bottle ( 5ml )", "Prednisolone 15 mg / 5 ml syrup", "Prednisolone 20 mg Tablet /Capsule", "Prednisolone 5 mg / 5 ml Syrup", "Prednisolone 5 mg Tablet /Capsule", "PREGABALIN 100 mg Capsule /Tablet", "PREGABALIN 100MG/5ML - Suspension", "PREGABALIN 150 mg capsule", "PREGABALIN 300MG - Capsule",
  "PREGABALIN 50MG - Capsule", "PREGABALIN 75 mg capsule", "Primaquine 15 mg tablet", "Primaquine 7.5 mg tablet", "Progesterone 100 mg - Vaginal Tablet", "Progesterone 100 mg Tablet", "progesterone 100 mg/2 ml - Ampoule", "Progesterone 200 mg - Vaginal/Rectal Pessary", "Progesterone 400 mg - Vaginal Pessary", "Progesterone gel - CRINONE 8% (Vag. Gel) Prefilled Applicators",
  "Propafenone 150mg tablet/capsule", "Propafenone 300mg - Film Coated Tablet", "Propofol 1% vial", "Propofol 2% - Ampoule or Vial", "Propolis + Chamomile + Zinc Oxide + Purified Honey -  Cream", "Propranolol 10mg - Tablet", "Propranolol 1mg/ml - Ampoule", "Propranolol 40mg - Tablet", "Propylthiouracil 50 mg Tablet", "PROCYCLIDINE 5 MG - Tablet",
  "Protamine Sulfate 10mg/ml vial", "Prucalopride 1mg - Film Coated Tablet", "Prucalopride 2mg - Tablet", "Psyllium seed, Ispaghula husk, Senna fruit tinnevelly, sachet", "Pumpkin Seed Oil 300mg - Capsule", "Pumpkin Seed Oil + Saw Palmetto Oil + Zinc - Capsule", "Pyrazinamide 500 mg - Tablet", "PYRIDOSTIGMINE 60MG - Tablet",
  "QUETIAPINE 100MG - Tablet", "QUETIAPINE 150MG - Film Coated Tablet", "QUETIAPINE 200 mg tablet/capsule", "QUETIAPINE 25MG - Film Coated Tablet", "QUETIAPINE 300MG XR - Film Coated Tablet", "QUETIAPINE 400MG XR - Film Coated Tablet", "QUETIAPINE 50MG - Film Coated Tablet", "Quinagolide 75 mcg tablet", "Quinine injection 600mg/2ml ampoule",
  "Quinine sulfate 300mg amp Units amp", "Quinine sulfate BP 300 mg tablet", "Rabeprazole 10mg Tablet", "Rabeprazole 20mg - Tablet", "Racecadotril 100mg -", "Racecadotril 10mg  sachet", "Racecadotril 30mg - (Children) sachet", "raltegravir 400mg Tab (isentress)", "Ramipril 1.25mg tablet", "Ramipril 10mg - Tablet",
  "Ramipril 10mg + Hydrochlorothiazide 25mg", "Ramipril 2.5mg - Cap/Tab", "Ramipril 2.5mg + Hydrochlorothiazide 12.5mg - Tablet", "Ramipril 5mg + Felodipine 5mg  Tablet", "Ramipril 5mg + Hydrochlorothiazide 25mg - Tablet", "Ramipril 5mg - Cap/Tab", "Ramucirumab 100 mg/10 ml vial", "Ramucirumab 500 mg/50 ml vial", "Ranibizumab 10 mg / ml",
  "Ranitidine 150 mg", "Ranitidine 150mg Eff. Sachets", "Ranitidine 300 mg", "Ranitidine 50 mg Injection", "Ranitidine 75 mg / 5 ml syrup", "RASAGILINE 1MG - Tablet", "Rebamipide 100mg", "Recombinant Hirudin 1120 IU/100 gm - Cream", "Recombinant Hirudin 1120 IU/100 gm - Gel", "Recombinant Hirudin 15mg - Ampoule",
  "Recombinant human chorionic gonadotropin 250mcg/0.5ml prefilled syringe", "Regorafenib 40 mg - Film Coated Tablet", "Remdesivir 100 mg (Solution) Vial", "Repaglinide 0.5mg Tablet", "Repaglinide 1mg Tablet", "Repaglinide 2 mg tablet", "Retinoic acid 0.05 % cream", "Ribavirin 200 mg", "RIBAVIRIN 400 mg capsule", "Ribociclib 200mg tablet",
  "rifampicin 150mg + isoniazide 75mg tab", "RIFAMPICIN 150MG", "rifampicin 2% 60 ml susp", "RIFAMPICIN 300MG", "RIFAMPICIN 300MG + ISONIAZID 150MG", "rifampicin, isoniazid and pyrazinamide combination", "rifampicin, isoniazid,  pyrazinamide and ethambutol combination", "RIFAXIMIN 200MG", "Rifaximin 550 mg - Tablet",
  "Rigenase (Wheat Extract) Cream", "Riluzole 50 mg - Tablet", "Ringer Acetate Infusion - Bottle (500ml)", "Ringer Acetate Infusion - Bottle (500ml) with Rubber Cap", "Ringer Infusion - Bottle (500ml)", "Ringer Infusion - Bottle (500ml) with Rubber Cap", "Ringer Lactate Infusion - Bottle (500ml)", "Ringer Lactate Infusion Bottle (500ml) with Rubber Cap",
  "Risdiplam 0.75mg/ml - Powder for oral solution", "RISPERIDONE 0.5MG", "Risperidone 1 mg", "Risperidone 1 mg / ml", "Risperidone 2 mg - Tablet", "RISPERIDONE 25MG - Vial", "Risperidone 3 mg - Tablet", "RISPERIDONE 37.5MG - Vial", "Risperidone 4 mg - Tablet", "ritonavir 100mg (Norvir 100mg)", "Rituximab 100 mg", "Rituximab 1400 mg",
  "Rituximab 500 mg/50 ml vial", "Rivaroxaban 10mg - Tablet", "Rivaroxaban 15mg - Tablet", "Rivaroxaban 2.5mg - Tablet", "Rivaroxaban 20mg - Tablet", "RIVASTIGMINE 1.5MG - Capsule", "RIVASTIGMINE 18MG - Transdermal Patch (10cm)", "RIVASTIGMINE 3MG - Capsule", "RIVASTIGMINE 4.5MG - Capsule", "RIVASTIGMINE 6MG - Capsule",
  "RIVASTIGMINE 9MG - Transdermal Patch (5cm)", "Rocuronium Bromide 50mg", "Romiplostim 250 mcg - Vial", "Rosuvastatin 10 mg - Tablet", "Rosuvastatin 20 mg - Tablet", "Rosuvastatin 5mg - Tablet", "Rutin 60mg + Vit.C 160mg - Tablet", "Ruxolitinib 15mg - Tablet", "Ruxolitinib 5mg - Tablet", "Sacubitril 24mg + Valsartan 26mg (50mg) - Film Coated Tablet",
  "Sacubitril 49mg + Valsartan 51mg (100mg) - Film Coated Tablet", "Sacubitril 97mg + Valsartan 103mg (200mg) - Film Coated Tablet", "Salbutamol 100 mcg + Beclomethasone 50 mcg aerosol", "Salbutamol 100 mcg / dose aerosol", "Salbutamol 2 mg", "Salbutamol 2 mg/5ml syrup", "Salbutamol 5 mg / ml For Nebulizing", "Salbutamol 8 mg SR",
  "Salbutamol - DISKUS 200 mcg/dose diskus", "Salicylic acid / Flumethasone Pivalate tube", "Salmeterol 25mcg/actuation - Inhalation", "Sapropterin Dihydrochloride 500mg sachet", "Sarilumab 200mg", "Scar removing gel or cream", "Secnidazole 500 mg Tablet", "Secukinumab 150 mg / ml", "Semaglutide 0.25 mg - Pen", "Semaglutide 0.5 mg - Pen",
  "Semaglutide 1 mg - Pen", "Senna Extract - Tablet", "SERTINDOLE 16MG - Tablet", "SERTINDOLE 20 mg - Tablet", "SERTINDOLE 4MG - Tablet", "Sertraline 50 mg - Tablet", "SERTRALINE 100MG", "Sevelamer 800mg - Tablet", "Sevoflurane 250ml (With Vaporizer for each 5000 bottle) + (1 filler per 1 bottle)", "Sildenafil 100mg - Tablet",
  "Sildenafil 25 mg - Tablet", "Sildenafil 50 mg - Tablet", "Silodosin 4mg - Capsule", "Silodosin 8mg - Capsule", "Silver pray", "Silver Sulfadiazine 1 % Cream", "Silymarin + soya lecithin + vitamin c + astragalus root PWD ext. + zinc capsule", "Silymarin + Ursodeoxycholic acid - Capsule", "Silymarin 140 mg -Tablet", "SILYMARIN 140 mg - Sachet",
  "SILYMARIN 200 mg - Capsule", "Silymarin suspension", "Simvastatin 10mg -Tablet", "Simvastatin 20mg - Tablet", "Simvastatin 40mg - Tablet", "Siponimod 0.25mg (Loading dose)", "Siponimod 2mg (Maintenance dose) (Rescue doses will be supplied FOC)", "Sirolimus 1 mg", "Sitagliptin 100 mg tablet", "Sitagliptin 50 mg / Metformin 1000 mg - Tablet",
  "Sitagliptin 50 mg / Metformin 500 mg - Tablet", "Sitagliptin 50 mg / Metformin 850 mg - Tablet", "Sitagliptin 50 mg - Tablet", "SmofKabiven (1300 Kcal/1904ml)] - 3 in 1 Chamber bag", "SmofKabiven (800 Kcal/1206 ml)] - 3 in 1 Chamber bag", "Sodium Alginate 5 g + Sodium Bicarbonate 2.5 g Suspenion",
  "Sodium Alginate 500mg + Potassium Bicarbonate 100mg/5ml - Suspension", "Sodium Alginate 500mg + Potassium Bicarbonate 267mg + Calcium Carbonate 160mg/10ml - Liquid Sachet", "Sodium Bicarbonate + Citric Acid + Tartaric Acid - Effervescent Granules Sachet", "Sodium Bicarbonate 1 kg", "Sodium Bicarbonate 8.4%",
  "Sodium Chloride + Sodium Bicarbonate + Borax Nasal Wash", "Sodium Chloride 0.45% Infusion - Bottle (500ml) with Rubber Cap", "Sodium Chloride 0.9% (Normal Saline) - Bag (3L)", "Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (100ml) with Rubber Cap", "Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (1L) with Rubber Cap",
  "Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (250ml) with Rubber Cap", "Sodium Chloride 0.9% (Normal Saline) Infusion - Bottle (500ml) with Rubber Cap", "Sodium Chloride 0.9% Infusion - Bottle (50 ml)", "Sodium Chloride 3% Infusion - Bottle (500ml)", "Sodium Glycerophosphate 1 mmol/ml vial", "Sodium Hyaluronate 2mg/ml - Bottle 10 ml Eye Drops",
  "Sodium Hyaluranate 20 mg / 2 ml Box/5", "Sodium Lactate 1/6 Mol Solution", "Sodium Picosulphate - Oral Drops", "Sodium Picosulphates, Light Magnesium Oxide & Citric Acid", "Sodium Polystyrene Sulfonate - 454 gm Jar", "SODIUM VALPROATE + VALPROIC ACID 500MG Tablet", "SODIUM VALPROATE 200MG", "SODIUM VALPROATE 200MG/5ML - Syrup",
  "Sodium Valporate 57.64mg/ml Syrup", "sodium chloride for Irrigation", "Sofosbuvir 400 mg + dacla Tablet", "Sofosbuvir 400 mg+ Ledipasvir 90 mg - Tablet", "Sofosbuvir 400 mg tablet", "Sofosbuvir+Velpatasvir 400/1000 mg - Tablet", "Sofosbuvir 400mg + Velpatasavir 100mg + Voxilaprevir 100mg - tablet", "Solifenacin 10mg - Film Coated Tablet",
  "Solifenacin 5mg - Film Coated Tablet", "Somatropin Prefilled Pen 5mg/1.5 ml", "Somattropin 10mg/1.5ml Cartridge", "Sorafenib 200 mg - Tablet", "Sotalol 80mg - Film Coated Tablet", "SPIRAMYCIN 1.5 M.I.U.", "SPIRAMYCIN 3 M.I.U.", "Spironolactone 100mg - Tablet", "Spironolactone 25 mg - Tablet", "Sterile Concentrate for Cardioplegia Infusion 20ml Vial",
  "Sterile water for injection - Ampoule", "Streptokinase 1,500,000 IU", "STREPTOMYCIN 1G", "Sugammadex 100mg/ml - Ampoule or Vial", "Sulbutiamine 400 mg", "SULFAMETHOXAZOLE 400MG + TRIMETHOPRIM 80MG", "SULFAMETHOXAZOLE 800MG + TRIMETHOPRIM 160MG - Cap/Tab", "Sulfamethoxazole 200 mg + Trimethoprim 40 mg ( /5ml)",
  "Sulfasalazine 500mg", "Sulpiride 50 mg - Tablet", "SULPIRIDE 200MG -Tablet", "SUMATRIPTAN 100MG - Tablet", "Sumatriptan up to 50 mg", "Sunitinib 12.5 mg-sutent 12.5mg", "Suxamethonium Chloride 100mg/5ml", "Syringe 1cm تنظيم الأسرة", "-Syringe 3cm تنظيم الأسرة", "Syrup containing calcium salt except calcium levulinate",
  "Tadalafil 20mg - Tablet", "Tadalafil 5mg - Tablet", "Tafluprost 15 mcg/ml - (30 doses)", "Tacrolimus 0.03% Ointment - Tube 15gm", "Tacrolimus 0.1% Ointment - Tube 30 gm", "Tacrolimus 0.5 mg - Cap/Tab", "Tacrolimus 1 mg - Tablet", "TAMOXIFEN 20 mg tablet", "Tamoxifen 10 mg tablet", "Tamsulosin + Solifenacin - Tablet", "Tamsulosin 0.4mg",
  "Tamsulosin 0.4mg PR - Prolonged Release Tablet", "Tazarotene 0.1 % Gel", "TEICOPLANIN 200MG - Vial + Solvent", "TEICOPLANIN 400MG - Vial + Solvent", "Tegafur 100 mg + Uracil 224 mg", "Telmisartan 40mg - Tablet", "Telmisartan 40mg + Hydrochlorothiazide 12.5mg - Tablet", "Telmisartan 80mg - Tablet", "Telmisartan 80mg + Hydrochlorothiazide 12.5mg - Tablet",
  "Telmisartan 80mg + Hydrochlorothiazide 25mg - Tablet", "Temozolomide 100 mg -Tablet", "Tenofovir 25mg - Tablet", "Tenofovir 300 mg - Tablet", "Tenoxicam 20mg - Cap/Tab", "TERBINAFINE 125MG - Cap/Tab", "TERBINAFINE 250MG - Cap/Tab", "Terbinafine HCL 1% - Cream", "Terbinafine HCL 1% - Spray", "Terbutaline 30 mg / 100 ml", "Terbutaline SO4 2.5 mg",
  "Teriflunomide 14mg - Tablet", "Teriparatide 600 mcg", "Terlipressin 1mg - Ampoule", "Testosterone 250 mg/ml - ampoule", "Testosterone undecanoate 1 gm/ 4 ml vial", "TETRACYCLINE 250MG - Cap/Tab", "Tetracycline 3 % Ointment", "Tetracycline 500 mg", "Tetracosactide 1 mg", "Tetrahydrozoline HCL - Eye drops", "Tetrahydrozoline HCL+ Zinc Sulphate - Eye drops",
  "Theophylline 200 mg - tablet", "Theophylline 300 mg - Tablet", "Theophylline 400 mg SR", "THIOCTIC ACID (ALPHA-LIPOIC ACID) 300MG", "THIOCTIC ACID (ALPHA-LIPOIC ACID) 600MG", "THIOCTIC ACID 300MG + VIT.B1 40MG + VIT.B12 250MCG - Capsule", "THIOCTIC ACID 600MG + VIT.B1 80MG + VIT.B12 500MCG - Film Coated taplet",
  "thiopental 500mg vial", "Thyme+Premula Extract - Syrup", "Thyroxine 100 mcg Tablet", "Thyroxine 50 mcg - Tablet", "TIANEPTINE 12.5MG - Tablet", "Tibolone 2.5 mg tablet", "Ticagrelor 60mg - Tablet", "Ticagrelor 90mg - Tablet", "Tiemonium methyl sulfate 5 mg/ 2ml ampule", "Tiemonium methylsulphate 10 mg / 5 ml", "Tiemonium methylsulphate 20 mg",
  "Tiemonium methylsulphate 50 mg", "TIGECYCLINE 50MG - Vial", "Timolol + Travoprost - 0.004/0.5 % eye drop", "Timolol 0.1 % Eye Gel", "Timolol 0.5%", "TIMOLOL 5 mg/ml+BIMATOPROST 0.3 mg/ml 3 ml bottle - Eye Drops", "TINIDAZOLE 500MG - Cap/Tab", "Tioconazole 1% - cream", "Tioconazole 2 % vag.cream", "Tioconazole100 mg Vaginal Tablet or Ovule",
  "Tiotropium bromide 18mcg", "Tiotropium bromide+Olodaterol hcl 2.5/2.5 mcg - Inhaher", "Tiponimod 0.25mg (Maintenance dose)", "Tirofiban 12.5mg/50ml - Vial", "Tixagevimab + Cilgavimab", "Tizanidine 2mg - Cap/Tab", "Tizanidine 4mg - Cap/Tab", "Tobramycin + Dexamethasone Eye Drops", "Tobramycin + Dexamethasone Eye Ointment", "Tobramycin 0.3 % eye drops",
  "Tobramycin 0.3 % eye oint", "Tocilizumab 200 mg / 10 ml", "Tocilizumab 400 mg / 20 ml", "Tocilizumab 80 mg / 4 ml", "Tofacitinib 5mg tablet", "Tolnaftate - Cream", "Tolterodine 2mg - Tablet", "Tolterodine 4mg", "Topotecan Hydrochloride 4 mg vial", "TOPIRAMATE 100MG - Tablet", "TOPIRAMATE 25MG - Tablet", "TOPIRAMATE 50MG - Tablet",
  "Torsemide 10 mg - Tablet", "Torsemide 10mg/1ml - Ampoule", "Torsemide 20 mg - Tablet", "Torsemide 5mg - Tablet", "Trace Element (for Adult)", "Trace Element (for Child)", "TRAJENTA 5 mg - Tablet", "Tramadol 100mg", "Tramadol 100mg SR - Cap/Tab", "Tramadol 150mg - Tablet", "Tramadol 200 mg - Tablet", "Tramadol 50 mg - Tablet", "Trandolapril/Verapamil 180/2 mg - tablet",
  "Tranexamic Acid 500mg - Ampoule (5ml)", "Tranexamic Acid 500mg - Film Coated Tablet", "Trastuzumab 440mg - Vial", "Trastuzumab 600 mg - S.C", "Travoprost 0.004 % Eye Drops 2.5 ml", "Trazodone 50 mg - Tablet", "Tress spray 120 ml", "TRETOFLAMIN 0.025 % cream", "Triamcinolone 4 mg", "Triamcinolone 40 mg/mg", "Triamcinolone Acetonide 55 mcg - Nasel Spray",
  "TRIFLUOPERAZINE 1MG", "TRIFLUOPERAZINE 5MG", "Trimebutine 100mg - Tablet", "Trimebutine 200mg - Tablet", "Trimebutine 24mg/5ml - Suspension", "Trimebutine 50mg - Ampoule", "Trimetazidine 20mg - Tablet", "Trimetazidine 35mg - Tablet", "Triptorelin 0.1 mg", "Triptorelin CR 3.75 mg CR", "Tropicamide 0.5 % Eye Drops", "Tropicamide 1 % Eye Drops",
  "Tropisetron 2mg/2ml", "Tropisetron 5mg/5mg", "Trospium chloride 20mg- Tablet", "Tuberculin PPD PT 5 TU/0.1ml Vial", "UCCMASAFE 4 litre", "UCCMATOL 5%", "Urofollitropin (Highly Purified Human Urinary Follicle Stimulating Hormone)", "URO-VAXOM 60 mg capsule", "Ursodeoxycholic Acid 250mg - Capsule", "Ursodeoxycholic acid 450 mg - Capsule",
  "Ustekinumab 130 mg/26ml - Vial", "Ustekinumab 45 mg/0.5 ml - Prefilled Syringe", "Ustekinumab 90 mg/ml - Prefilled Syringe", "VAGIPROST 25 mcg vaginal tablet", "Valacyclovir 1000 mg - Tablet", "Valganciclovir 450 mg -valcyte", "Valsartan 160 mg + Hydrochlorothiazide 25 mg", "Valsartan 160mg - Tablet", "Valsartan 160mg + Hydrochlorothiazide 12.5mg",
  "Valsartan 320mg + Hydrochlorothiazide 12.5mg", "Valsartan 320mg + Hydrochlorothiazide 25mg", "Valsartan 320mg - Tablet", "Valsartan 40mg - Tablet", "Valsartan 80mg + Hydrochlorothiazide 12.5mg", "Valsartan 80mg - Tablet", "VANCOMYCIN 1 gm vial", "VANCOMYCIN 500MG", "VARENICLINE 0.5MG - Tablet", "VARENICLINE 1MG - Tablet",
  "Vaseline 1 kg", "Vecuronium Br 4 mg", "Vedolizumab 300 mg vial", "Venclexta 100mg Tablet", "Venclexta Starting Pack (14 Tablet / 10mg + 7 Tablet / 50mg + 21 Tablet / 100mg)", "VENLAFAXINE 150MG - Capsule", "Venlafaxine 150 mg capsule", "VENLAFAXINE 75MG - Capsule", "Venlafaxine 37.5 mg", "Verapamil 240mg SR - Film Coated Tablet",
  "Verapamil 40mg/5ml - Ampoule", "Verapamil 5mg/2ml - Ampoule", "Verapamil 80mg - Film Coated Tablet", "VICTOZA 6 mg/ml pen", "Vidaza 100mg Vial", "Vildagliptin 50 mg - Tablet", "Vinblastine 10 mg / 10 ml", "VINCAMINE 30MG - Capsule", "Vincristine 1 mg / 1 ml vial", "Vincristine 2 mg / 2 ml vial", "Vinorelbine 10 mg vial", "Vinorelbine 20 mg",
  "Vinorelbine 30 mg", "Vinorelbine 50 mg", "Vinorelbine 50 mg - Vial 5 ml", "Viotic (Clioquinol 1 % + Flumethasone 0.02 %) - Ear Drops", "Vitamin (B12) 1 mg", "Vitamin (B12) 1 mg Injection", "Vitamin A Up To 50.000 I.U.", "Vitamin A (Retinol Palmitate) 10.000 mg/gm- Eye Gel", "Vitamin B containing at least B1 (100 mg ), B6 (50 mg ),B12 (250 mcg )",
  "vitamin B complex ampoule - IV/IM - Free of benzyl alchol", "Vitamin C (Asorbic Acid) 500 mg", "Vitamin C 1 gm", "Vitamin E - BE ACTIVE Capsule", "Vitamine E 400 mg", "Vitamin E FORTE capsule", "Vitamins Containing Selenium", "VORICONAZOLE 200MG - Cap/Tab", "VORICONAZOLE 200MG - Vial", "VORICONAZOLE 50MG - Cap/Tab", "VORTIOXETINE 10MG - Film Coated Tablet -",
  "VORTIOXETINE 20MG - Film Coated Tablet -", "Warfarin 1 mg", "Warfarin 2 mg", "Warfarin 3 mg", "Warfarin 5mg", "Water Soluble Vitamins (Biotin + Folic acid + Nicotinamide)", "Xylometazoline hydrochloride - Infaintile Nasal Drops", "ZALEPLON 10MG - Tablet", "ZALEPLON 5MG - Tablet", "ZEROL Cream 40 gm",
  "zidovudine 50mg/5ml", "Zinc 25mg Tablet", "Zinc Oxide + Olive Oil Cream 75 gm", "Zinc Oxide 20 %", "Zinc Sulphate 10 mg / 5 ml Powder for Syrup", "Zinc Sulphate 20 mg / 5 ml Powder for Syrup", "Zoledronic Acid 4 mg / 5 ml - Vial", "Zoledronic Acid 5 mg /100 ml- Vial", "ZOLEDRONIC ACID ANHYDROUS 0.8 mg/ml - Vial", "Zolmitriptan 2.5 mg - Cap/ Tab",
  "Zonisamide 100mg - Capsule", "Zonisamide 25mg - Capsule", "Zonisamide 50mg - Capsule",
]).values()].sort();

const commonSymptoms = [
    'Fever', 'Chills', 'Cough', 'Shortness of breath (Dyspnea)', 'Fatigue', 'Muscle or body aches', 'Headache', 'Sore throat',
    'Congestion or runny nose', 'Nausea', 'Vomiting', 'Diarrhea', 'Abdominal pain', 'Chest pain', 'Palpitations', 'Dizziness',
    'Lightheadedness', 'Syncope (fainting)', 'Confusion', 'Altered mental status', 'Seizure', 'Weakness (generalized)',
    'Numbness or tingling (paresthesia)', 'Rash', 'Itching (pruritus)', 'Swelling (edema)', 'Joint pain (arthralgia)',
    'Back pain', 'Difficulty swallowing (dysphagia)', 'Painful urination (dysuria)', 'Increased frequency of urination',
    'Blood in urine (hematuria)', 'Constipation', 'Loss of appetite', 'Weight loss', 'Weight gain', 'Anxiety', 'Depression',
    'Insomnia', 'Bleeding', 'Bruising', 'Jaundice (yellowing of skin/eyes)', 'Blurred vision', 'Hoarseness'
].sort();


document.addEventListener("DOMContentLoaded", () => {
    puter.init(); 
    populateCaseSelector();
    populateVitals();
    populateLabs();
    populateDatalists();
    document.getElementById("caseForm").style.display = "none";
});

async function generateAIReport() {
    if (!currentCaseId) {
        alert('Please select a case to analyze.');
        return;
    }

    const aiProvider = document.getElementById('aiProviderSelect').value;
    const aiContent = document.getElementById('aiDashboardContent');
    const loadingMessage = aiProvider === 'puter' ? 'Analyzing with Puter (OpenAI)... Please wait.' : 'Analyzing with Groq... Please wait.';
    
    aiContent.innerHTML = `<div class="loading"><div class="spinner"></div><p>${loadingMessage}</p></div>`;
    switchTab('aiDashboard');

    const caseData = collectCaseData();
    const prompt = `You are a highly experienced professor of clinical pharmacy. Analyze the following patient case data.

IMPORTANT: Provide your analysis as a single, valid JSON object. Do not include any text outside of the JSON object. The JSON object must have the following keys: "diagnosis", "efficacy", "drugChanges", "interactions", "progress", "recommendations", "references".

- diagnosis: Explain the likely diagnosis with your rationale. - efficacy: Evaluate the efficacy of the included drugs. - drugChanges: Suggest drugs to be included or excluded, with a clear rationale for each. - interactions: Identify potential drug-drug interactions and suggest management. - progress: Analyze the patient's progress. Present this as a Markdown table with columns for "Date/Follow-up", "Key-Metric (e.g., TLC, Temp)", "Value", and "Comment". - recommendations: Provide a final list of clear, actionable recommendations. - references: List any medical journals, guidelines, or studies used to support your rationale.

Here is the patient data: ${JSON.stringify(caseData, null, 2)}`;

    try {
        let rawContent;

        if (aiProvider === 'puter') {
            // --- PUTER.JS (OPENAI) PATH ---
            // CORRECTION: Changed from the incorrect puter.ai.run to the correct API call
            const result = await puter.ai.chat.completions.create({
                model: 'openai:gpt-3.5-turbo',
                messages: [{ "role": "user", "content": prompt }],
                response_format: { type: 'json_object' }
            });
            rawContent = result.choices[0].message.content;
        } else {
            // --- GROQ PATH (Original logic) ---
            const API_URL = 'https://api.groq.com/openai/v1/chat/completions';
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${GROQ_API_KEY}`
                },
                body: JSON.stringify({
                    "model": GROQ_MODEL,
                    "messages": [{ "role": "user", "content": prompt }],
                    "response_format": { "type": "json_object" }
                })
            });

            if (!response.ok) {
                const errorBody = await response.json();
                throw new Error(`API Error (${response.status}): ${errorBody.error.message}`);
            }
            const result = await response.json();
            rawContent = result.choices[0].message.content;
        }

        let aiData = JSON.parse(rawContent);
        renderAIDashboard(aiData);

    } catch (err) {
        aiContent.innerHTML = `<div class="error-message"><h4>Analysis Failed</h4><p>${err.message}</p></div>`;
    }
}

function populateDatalists() {
    const symptomsDatalist = document.getElementById('symptoms-list');
    const drugsDatalist = document.getElementById('drugs-list');
    symptomsDatalist.innerHTML = '';
    drugsDatalist.innerHTML = '';
    commonSymptoms.forEach(symptom => { const option = document.createElement('option'); option.value = symptom; symptomsDatalist.appendChild(option); });
    hospitalDrugList.forEach(drug => { const option = document.createElement('option'); option.value = drug; drugsDatalist.appendChild(option); });
}

function createRowTemplates(type) {
    const deleteBtn = `<td><button type="button" class="btn-delete" onclick="deleteRow(this)">🗑️</button></td>`;
    const templates = {
        medRecon: `<td><input type="text" list="drugs-list"/></td><td><input type="text"/></td><td><input type="text"/></td><td><select><option>Yes</option><option>No</option></select></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`,
        problemList: `<td><input type="text" list="symptoms-list"/></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`,
        infusion: `<td><input type="text" list="drugs-list"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`,
        antibiotics: `<td><input type="text" list="drugs-list"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`,
        otherMeds: `<td><input type="text" list="drugs-list"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`,
        progressNotes: `<td><input type="date"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td><td><input type="text"/></td>${deleteBtn}`
    };
    return templates[type] || '';
}

function deleteRow(btn) {
    btn.closest('tr').remove();
}

const aiTabsInfo = [ { key: 'diagnosis', title: 'Diagnosis & Rationale' }, { key: 'efficacy', title: 'Drug Efficacy' }, { key: 'drugChanges', title: 'Drug Inclusions/Exclusions' }, { key: 'interactions', title: 'Drug-Drug Interactions' }, { key: 'progress', title: 'Progress Chart' }, { key: 'recommendations', title: 'Recommendations' }, { key: 'references', title: 'References' } ];
function renderAIDashboard(data) { const container = document.getElementById('aiDashboardContent'); const tabButtons = aiTabsInfo.map((tab, index) => `<div class="ai-tab ${index === 0 ? 'active' : ''}" data-tab-key="${tab.key}" onclick="switchAiTab('${tab.key}')">${tab.title}</div>`).join(''); const tabPanels = aiTabsInfo.map((tab, index) => `<div class="ai-tab-panel ${index === 0 ? 'active' : ''}" id="ai-panel-${tab.key}"><pre>${data[tab.key] || 'No information provided for this section.'}</pre></div>`).join(''); container.innerHTML = `<div class="ai-tab-container">${tabButtons}</div><div class="ai-tab-content-wrapper">${tabPanels}</div><button type="button" class="btn btn-export" onclick="exportAIDashboard()">📄 Export Report as PDF</button>`; }
function switchAiTab(tabKey) { document.querySelectorAll('.ai-tab').forEach(tab => tab.classList.remove('active')); document.querySelectorAll('.ai-tab-panel').forEach(panel => panel.classList.remove('active')); document.querySelector(`.ai-tab[data-tab-key="${tabKey}"]`).classList.add('active'); document.getElementById(`ai-panel-${tabKey}`).classList.add('active'); }
function exportAIDashboard() { const container = document.getElementById('aiDashboardContent'); if (!container.querySelector('.ai-tab-container')) { alert('No AI report to export. Please generate one first.'); return; } const reportElement = document.createElement('div'); reportElement.style.padding = '20px'; reportElement.style.fontFamily = 'Segoe UI, Tahoma, Geneva, Verdana, sans-serif'; let reportHtml = `<h1>AI Clinical Pharmacy Report: Case ${currentCaseId}</h1>`; aiTabsInfo.forEach(tab => { const panel = document.getElementById(`ai-panel-${tab.key}`); if (panel) { const panelContent = panel.querySelector('pre').innerText; reportHtml += `<hr><h2>${tab.title}</h2><p style="white-space: pre-wrap; line-height: 1.6;">${panelContent}</p>`; } }); reportElement.innerHTML = reportHtml; const opt = { margin: 10, filename: `AI_Report_${currentCaseId}_${new Date().toISOString().slice(0,10)}.pdf`, image: { type: 'jpeg', quality: 0.98 }, html2canvas: { scale: 2, useCORS: true }, jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' } }; html2pdf().set(opt).from(reportElement).save(); }
const vitalsList=[{key:"bp",name:"BP (mmHg)"},{key:"hr",name:"HR (bpm)"},{key:"rr",name:"RR (br/min)"},{key:"spo2",name:"SpO₂ (%)"},{key:"o2status",name:"O₂ status"},{key:"temp",name:"Temp (°C)"},{key:"rbs",name:"RBS (mg/dL)"},{key:"cvp",name:"CVP (cmH2O)"},{key:"gcs",name:"GCS"},{key:"fluidIntake",name:"Fluid intake (mL)"},{key:"uop",name:"UOP(Urinary Output) (mL)"},{key:"dayBalance",name:"Day Balance (mL)"},{key:"passStool",name:"Pass stool"}];
const labsList=[{group:"CBC"},{key:"tlc",name:"TLC",normal:"4-11 x10³/μL"},{key:"rbcs",name:"RBCs",normal:"4.5-5.5 x10⁶/μL"},{key:"hct",name:"Hct (%)",normal:"40-52"},{key:"hemoglobin",name:"Hemoglobin (g/dL)",normal:"13.5-17.5"},{key:"platelets",name:"Platelets",normal:"150-450 x10³/μL"},{group:"Coagulation"},{key:"pt",name:"P.T",normal:"11-13.5 s"},{key:"ptt",name:"P.T.T",normal:"25-35 s"},{key:"inr",name:"INR",normal:"0.8-1.2"},{key:"procalcitonin",name:"Procalcitonin",normal:"<0.1 ng/mL"},{group:"Liver Function"},{key:"albumin",name:"Albumin",normal:"3.5-5.5 g/dL"},{key:"tBilirubin",name:"T. Bilirubin",normal:"0.1-1.2 mg/dL"},{key:"dBilirubin",name:"D. Bilirubin",normal:"0-0.3 mg/dL"},{key:"alt",name:"ALT (SGPT)",normal:"7-56 U/L"},{key:"ast",name:"AST (SGOT)",normal:"10-40 U/L"},{key:"alkPhosph",name:"Alk. Phosph.",normal:"44-147 IU/L"},{group:"Kidney Function"},{key:"urea",name:"Urea",normal:"7-20 mg/dL"},{key:"creatinine",name:"Creatinine",normal:"0.6-1.3 mg/dL"},{key:"crcl",name:"CrCl",normal:">90 mL/min"},{key:"uricAcid",name:"Uric acid",normal:"3.5-7.2 mg/dL"}];
function populateVitals(savedVitals = {}) { const tbody = document.getElementById('vitalsRows'); tbody.innerHTML = ''; vitalsList.forEach(vital => { const row = tbody.insertRow(); row.innerHTML = `<th scope="row">${vital.name}</th>${Array.from({ length: 6 }).map((_, i) => `<td><input type="text" data-key="${vital.key}" data-day="${i}" value="${savedVitals[vital.key]?.[i] || ''}"></td>`).join('')}`; }); }
function populateLabs(savedLabs = {}) { const tbody = document.getElementById('labRows'); tbody.innerHTML = ''; labsList.forEach(lab => { if (lab.group) { const groupRow = tbody.insertRow(); groupRow.className = 'lab-group-header'; groupRow.innerHTML = `<th colspan="7">${lab.group}</th>`; } else { const row = tbody.insertRow(); row.innerHTML = `<th scope="row">${lab.name}</th><td>${lab.normal}</td>${Array.from({ length: 5 }).map((_, i) => `<td><input type="text" data-key="${lab.key}" data-day="${i}" value="${(savedLabs[lab.key] || [])[i] || ''}"></td>`).join('')}`; } }); }
function addRow(tableBodyId,type){const tableBody=document.getElementById(tableBodyId);const newRow=tableBody.insertRow();newRow.innerHTML=createRowTemplates(type)}
function populateCaseSelector(){const selector=document.getElementById("caseSelect");const currentSelection=selector.value;selector.innerHTML='<option value="">-- Select Existing Case --</option>';Object.keys(cases).sort().forEach(id=>{const option=document.createElement("option");option.value=id;option.textContent=`${id} - ${cases[id].demographics?.patientName||"Unnamed Case"}`;selector.appendChild(option)});selector.value=currentSelection}
function createNewCase(){const newCaseIdInput=document.getElementById("newCaseId");const newCaseId=newCaseIdInput.value.trim();if(!newCaseId){alert("Please enter a Case ID");return}if(cases[newCaseId]){alert("This Case ID already exists.");return}currentCaseId=newCaseId;cases[currentCaseId]={demographics:{patientId:newCaseId},clinicalInfo:{},medRecon:{source:"",rows:[]},problemList:[],vitals:{},labs:{},infusions:[],antibiotics:[],otherMeds:[],progressNotes:[]};newCaseIdInput.value="";populateCaseSelector();document.getElementById("caseSelect").value=currentCaseId;loadCase();switchTab("demographics")}
function loadCase(){currentCaseId=document.getElementById("caseSelect").value;const aiButton=document.getElementById("aiReportBtn");if(!currentCaseId){document.getElementById("caseForm").style.display="none";aiButton.disabled=true;return}aiButton.disabled=false;document.getElementById("caseForm").style.display="block";const data=cases[currentCaseId]||{};document.getElementById("mainForm").reset();document.querySelectorAll("tbody").forEach(tbody=>tbody.innerHTML="");const demo=data.demographics||{};Object.keys(demo).forEach(key=>{const el=document.getElementById(key);if(el)el.value=demo[key]||""});const clinical=data.clinicalInfo||{};Object.keys(clinical).forEach(key=>{const el=document.getElementById(key);if(el)el.value=clinical[key]||""});const loadTable=(tbodyId,rowData,templateType)=>{const tbody=document.getElementById(tbodyId);(rowData||[]).forEach(rowDataItem=>{const newRow=tbody.insertRow();newRow.innerHTML=createRowTemplates(templateType);newRow.querySelectorAll("input, select").forEach((input,i)=>{input.value=rowDataItem[i]||""})})};document.getElementById("medReconSource").value=data.medRecon?.source||"";loadTable("medReconRows",data.medRecon?.rows,"medRecon");loadTable("problemListRows",data.problemList,"problemList");loadTable("infusionRows",data.infusions,"infusion");loadTable("antibioticsRows",data.antibiotics,"antibiotics");loadTable("otherMedsRows",data.otherMeds,"otherMeds");loadTable("progressNotesRows",data.progressNotes,"progressNotes");populateVitals(data.vitals||{});populateLabs(data.labs||{});document.getElementById("aiDashboardContent").innerHTML=`<h3>🤖 AI-Powered Clinical Analysis</h3>\n    <p>Click "Generate AI Report" to analyze the current case data. The analysis will appear here.</p>`}
function saveCase(){if(!currentCaseId)return;const data={demographics:{},clinicalInfo:{}};document.querySelectorAll("#demographics input, #demographics select, #demographics textarea").forEach(el=>{if(el.id){const sectionHeader=el.closest(".section").querySelector("h3").textContent;if(sectionHeader.includes("Patient Information")){data.demographics[el.id]=el.value}else if(sectionHeader.includes("Clinical Information")){data.clinicalInfo[el.id]=el.value}}});const saveTable=tbodyId=>{return Array.from(document.querySelectorAll(`#${tbodyId} tr`)).map(row=>Array.from(row.querySelectorAll("input, select")).map(input=>input.value))};data.medRecon={source:document.getElementById("medReconSource").value,rows:saveTable("medReconRows")};data.problemList=saveTable("problemListRows");data.infusions=saveTable("infusionRows");data.antibiotics=saveTable("antibioticsRows");data.otherMeds=saveTable("otherMedsRows");data.progressNotes=saveTable("progressNotesRows");data.vitals={};document.querySelectorAll("#vitalsRows input").forEach(input=>{data.vitals[input.dataset.key]=data.vitals[input.dataset.key]||[];data.vitals[input.dataset.key][input.dataset.day]=input.value});data.labs={};document.querySelectorAll("#labRows input").forEach(input=>{data.labs[input.dataset.key]=data.labs[input.dataset.key]||[];data.labs[input.dataset.key][input.dataset.day]=input.value});cases[currentCaseId]=data;localStorage.setItem("pharmaCases",JSON.stringify(cases));populateCaseSelector();document.getElementById("caseSelect").value=currentCaseId;alert("Case saved successfully!")}

function switchTab(tabId){
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    document.querySelectorAll(".tab-content").forEach(c=>c.classList.remove("active"));
    document.querySelector(`[onclick="switchTab('${tabId}')"]`).classList.add("active");
    document.getElementById(tabId).classList.add("active");

    if (tabId === 'manageCases') {
        populateCaseManager();
    }
}

function populateCaseManager() {
    const listContainer = document.getElementById('case-management-list');
    listContainer.innerHTML = ''; 

    const sortedCaseIds = Object.keys(cases).sort();

    if (sortedCaseIds.length === 0) {
        listContainer.innerHTML = '<p>No saved cases found.</p>';
        return;
    }

    sortedCaseIds.forEach(id => {
        const caseName = cases[id].demographics?.patientName || "Unnamed Case";
        const item = document.createElement('div');
        item.className = 'case-manager-item';
        item.innerHTML = `
            <span>${id} - ${caseName}</span>
            <button class="btn-delete" onclick="deleteCase('${id}')">Delete Case</button>
        `;
        listContainer.appendChild(item);
    });
}

function deleteCase(caseId) {
    if (confirm(`Are you sure you want to permanently delete case "${caseId}"? This action cannot be undone.`)) {
        delete cases[caseId];
        localStorage.setItem('pharmaCases', JSON.stringify(cases));
        
        if (currentCaseId === caseId) {
            currentCaseId = null;
            document.getElementById("caseForm").style.display = "none";
            document.getElementById("aiReportBtn").disabled = true;
        }

        populateCaseSelector();
        populateCaseManager();
        alert(`Case "${caseId}" has been deleted.`);
    }
}

function collectCaseData(){const data=cases[currentCaseId]||{};return{demographics:data.demographics,clinicalInfo:data.clinicalInfo,medRecon:data.medRecon,problemList:data.problemList,labs:formatLabDataForAI(data.labs),vitals:formatVitalDataForAI(data.vitals),antibiotics:data.antibiotics,otherMeds:data.otherMeds,progressNotes:data.progressNotes}}
function formatLabDataForAI(rawLabs={}){const formatted={};labsList.forEach(lab=>{if(lab.key&&rawLabs[lab.key]){const values=rawLabs[lab.key].filter(v=>v);if(values.length>0){formatted[lab.name]={normal:lab.normal,values:values}}}});return formatted}
function formatVitalDataForAI(rawVitals={}){const formatted={};vitalsList.forEach(vital=>{if(rawVitals[vital.key]){const values=rawVitals[vital.key].filter(v=>v);if(values.length>0){formatted[vital.name]=values}}});return formatted}

</script>
</body>
</html>
