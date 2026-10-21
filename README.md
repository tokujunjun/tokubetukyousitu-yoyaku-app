<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>学校施設・特別教室利用予約システム</title>
  <!-- Bootstrap 5 CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <!-- Bootstrap Icons -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.0/font/bootstrap-icons.css" rel="stylesheet">
  <!-- Animate.css -->
  <link href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css" rel="stylesheet">
  <!-- html2pdf.js (PDF保存用ライブラリ) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
  
  <style>
    :root {
      --card-radius: 12px;
      --sat-color: #0d6efd;
      --sat-text: #0d6efd;
      --sat-bg: #e7f1ff;
      --sun-color: #dc3545;
      --sun-text: #dc3545;
      --sun-bg: #ffe8e8;
      --holiday-color: #d63384;
      --hol-color: #d63384;
      --hol-text: #d63384;
      --holiday-bg: #f8d7da;
      --hol-bg: #f8d7da;
      --closed-color: #6f42c1;
      --closed-bg: #f3e8ff;
      --closed-border: #d8b4fe;
      --school-hol-text: #6f42c1;
      --school-hol-bg: #f3e8ff;
      --school-hol-border: #d8b4fe;
    }

    body {
      background-color: #f8f9fa;
      font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', 'Meiryo', sans-serif;
      color: #333;
      font-size: 1.02rem;
    }

    /* ヘッダーデザイン */
    .header-banner, .app-header {
      background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
      color: white;
      padding: 1.25rem 1rem;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      border-bottom: 4px solid #0d6efd;
    }

    .header-title, .app-title {
      font-weight: 700;
      font-size: 1.6rem;
      letter-spacing: 0.5px;
    }

    /* ビュー切替タブ */
    .view-tab .btn, .nav-pills-custom .nav-link {
      font-weight: 600;
      border-radius: 20px;
      padding: 0.4rem 1.2rem;
      margin-right: 0.3rem;
      transition: all 0.25s ease;
      color: #495057;
      background-color: #e9ecef;
    }

    .nav-pills-custom .nav-link.active {
      background-color: #0d6efd;
      color: white;
      box-shadow: 0 4px 10px rgba(13, 110, 253, 0.3);
    }

    /* 日付表示バッジ */
    .day-sat { background-color: var(--sat-bg) !important; color: var(--sat-text) !important; }
    .day-sun { background-color: var(--sun-bg) !important; color: var(--sun-text) !important; }
    .day-holiday { background-color: var(--hol-bg) !important; color: var(--hol-text) !important; font-weight: bold; }
    .day-school-holiday { background-color: var(--school-hol-bg) !important; color: var(--school-hol-text) !important; border: 1px solid var(--school-hol-border) !important; font-weight: bold; }

    /* タイムテーブル */
    .table-timetable, .timetable-container {
      background: white;
      border-radius: 12px;
      overflow: hidden;
      box-shadow: 0 2px 8px rgba(0,0,0,0.06);
    }

    .table-schedule {
      table-layout: fixed;
      min-width: 980px;
      margin-bottom: 0;
    }

    .table-timetable th, .table-schedule th {
      background-color: #f1f5f9;
      color: #334155;
      text-align: center;
      vertical-align: middle;
      font-weight: 700;
      padding: 12px 8px;
    }

    .table-schedule td {
      height: 100px;
      vertical-align: top;
      padding: 6px;
      background-color: #ffffff;
      border: 1px solid #dee2e6;
      position: relative;
    }

    .period-header {
      width: 140px;
      background-color: #e9ecef !important;
      color: #212529 !important;
      font-weight: bold;
      text-align: center;
      vertical-align: middle !important;
      font-size: 0.95rem;
      line-height: 1.3;
    }

    .period-time-text {
      font-size: 0.8rem;
      color: #6c757d;
      font-weight: normal;
      display: block;
      margin-top: 2px;
    }

    /* 月間施設マトリックス用 */
    .monthly-facility-table {
      min-width: 1100px;
    }
    .monthly-facility-table td {
      height: 75px;
      padding: 4px;
    }
    .day-cell-header {
      width: 140px;
      background-color: #f8f9fa !important;
      color: #333 !important;
      font-weight: bold;
      text-align: center;
      vertical-align: middle !important;
    }
    .day-cell-header.is-weekend-sat {
      background-color: var(--sat-bg) !important;
      color: var(--sat-color) !important;
    }
    .day-cell-header.is-weekend-sun {
      background-color: var(--sun-bg) !important;
      color: var(--sun-color) !important;
    }
    .day-cell-header.is-national-holiday {
      background-color: var(--holiday-bg) !important;
      color: var(--holiday-color) !important;
    }
    .day-cell-header.is-school-closed {
      background-color: var(--closed-bg) !important;
      color: var(--closed-color) !important;
      border-left: 5px solid var(--closed-color) !important;
    }

    /* 予約カード */
    .booking-card {
      border-left: 5px solid #0d6efd;
      background-color: #eef5ff;
      border-radius: 8px;
      padding: 6px 8px;
      height: 100%;
      cursor: pointer;
      transition: all 0.2s ease-in-out;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      box-shadow: 0 2px 5px rgba(0,0,0,0.05);
    }

    .booking-card:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 14px rgba(0,0,0,0.15);
      filter: brightness(0.97);
    }

    .booking-card .class-badge {
      font-weight: 700;
      font-size: 0.9rem;
      color: #0d6efd;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .booking-card .teacher-name {
      font-size: 0.8rem;
      color: #495057;
      font-weight: 600;
    }

    .booking-card .purpose-text {
      font-size: 0.75rem;
      color: #6c757d;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* 空きセル追加ボタン */
    .empty-slot-btn {
      width: 100%;
      height: 100%;
      border: 2px dashed #ced4da;
      border-radius: 8px;
      background: transparent;
      color: #adb5bd;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      transition: all 0.2s ease;
      font-size: 0.8rem;
      font-weight: 600;
    }

    .empty-slot-btn:hover {
      border-color: #0d6efd;
      color: #0d6efd;
      background-color: rgba(13, 110, 253, 0.05);
    }

    /* 月間カレンダー専用スタイル */
    .monthly-calendar-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 8px;
    }
    .calendar-day-head {
      text-align: center;
      font-weight: bold;
      padding: 10px;
      background-color: #343a40;
      color: white;
      border-radius: 8px;
    }
    .calendar-day-head.sat { background-color: var(--sat-color); }
    .calendar-day-head.sun { background-color: var(--sun-color); }

    .calendar-day-cell {
      background: white;
      border-radius: 10px;
      min-height: 125px;
      padding: 8px;
      border: 1px solid #dee2e6;
      box-shadow: 0 2px 6px rgba(0,0,0,0.03);
      cursor: pointer;
      transition: all 0.2s ease;
      display: flex;
      flex-direction: column;
    }
    .calendar-day-cell:hover {
      border-color: #0d6efd;
      transform: translateY(-2px);
      box-shadow: 0 6px 14px rgba(0,0,0,0.1);
    }
    .calendar-day-cell.other-month {
      background-color: #f8f9fa;
      opacity: 0.4;
      cursor: default;
    }
    .calendar-day-cell.is-saturday {
      background-color: #f4f8ff;
    }
    .calendar-day-cell.is-sunday {
      background-color: #fff5f5;
    }
    .calendar-day-cell.is-holiday {
      background-color: #fff0f5;
      border-color: #f8b4c8;
    }
    .calendar-day-cell.is-closed {
      background-color: var(--closed-bg) !important;
      border: 1px solid var(--closed-border) !important;
    }
    .calendar-day-cell.is-today {
      border: 2px solid #0d6efd !important;
      box-shadow: 0 0 10px rgba(13, 110, 253, 0.2);
    }

    .calendar-date-number {
      font-weight: bold;
      font-size: 1.1rem;
      margin-bottom: 4px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .calendar-booking-badge {
      font-size: 0.75rem;
      padding: 3px 6px;
      border-radius: 4px;
      margin-bottom: 3px;
      color: white;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      display: block;
    }

    /* 休日のタグ */
    .holiday-tag {
      font-size: 0.72rem;
      background-color: #dc3545;
      color: white;
      padding: 2px 6px;
      border-radius: 10px;
      font-weight: bold;
      display: inline-block;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      max-width: 100px;
    }

    .closed-day-tag {
      font-size: 0.72rem;
      background-color: var(--closed-color);
      color: white;
      padding: 2px 6px;
      border-radius: 10px;
      font-weight: bold;
      display: inline-block;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      max-width: 110px;
    }

    .fade-in {
      animation: fadeIn 0.25s ease-in-out;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(5px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .facility-badge {
      display: inline-block;
      padding: 4px 12px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: bold;
      color: white;
    }

    .toast-container {
      position: fixed;
      bottom: 20px;
      right: 20px;
      z-index: 1090;
    }

    @media print {
      .no-print { display: none !important; }
      body { background-color: white; font-size: 11pt; }
      .container-fluid { width: 100% !important; padding: 0 !important; }
      .timetable-container { box-shadow: none; border: 1px solid #000; }
      .table-schedule th, .table-schedule td { border: 1px solid #000 !important; }
      .booking-card { border: 1px solid #333 !important; box-shadow: none !important; background-color: #f8f9fa !important; }
      .empty-slot-btn { display: none !important; }
      .print-header { display: block !important; text-align: center; margin-bottom: 20px; }
    }

    .print-header { display: none; }
  </style>
</head>
<body>

  <!-- 印刷用タイトル -->
  <div class="print-header">
    <h2 id="printTitle">学校施設利用予約状況</h2>
    <p id="printSubTitle" class="fs-5 text-muted"></p>
  </div>

  <header class="app-header no-print">
    <div class="container-fluid d-flex flex-wrap justify-content-between align-items-center gap-3">
      <div class="d-flex align-items-center gap-2">
        <span class="fs-1">🏫</span>
        <div>
          <h1 class="h3 mb-0 app-title">学校施設・特別教室 利用予約システム</h1>
          <small class="opacity-75">校務のデジタル化・業務効率化支援 - PDF保存 / カレンダー連携対応</small>
        </div>
      </div>
      <div class="d-flex align-items-center gap-2 flex-wrap">
        <button class="btn btn-light btn-lg fw-bold shadow-sm" onclick="openNewBookingModal()">
          <i class="bi bi-plus-circle-fill text-success me-1"></i> 新規予約
        </button>
        <button class="btn btn-purple btn-lg fw-bold text-white" style="background-color: var(--closed-color);" onclick="openClosedDayManagerModal()">
          <i class="bi bi-building-fill-lock me-1"></i> 学校閉庁日
        </button>
        <button class="btn btn-warning btn-lg fw-bold text-dark" onclick="openPeriodManagerModal()">
          <i class="bi bi-clock-history me-1"></i> 時限設定
        </button>
        <button class="btn btn-outline-light btn-lg fw-bold" onclick="openFacilityManagerModal()">
          <i class="bi bi-gear-fill me-1"></i> 施設設定
        </button>
        <button class="btn btn-danger btn-lg fw-bold text-white" onclick="exportToPDF()" title="画面をPDF（画像・文書ファイル）で保存">
          <i class="bi bi-file-earmark-pdf-fill me-1"></i> PDF保存
        </button>
        <button class="btn btn-outline-light btn-lg" onclick="exportToCSV()" title="Excel（エクセル）で開けるデータを出力">
          <i class="bi bi-file-earmark-spreadsheet-fill me-1"></i> CSV（Excel用）
        </button>
        <button class="btn btn-outline-light btn-lg" onclick="window.print()" title="印刷画面を開く">
          <i class="bi bi-printer-fill me-1"></i> 印刷
        </button>
        <button class="btn btn-outline-danger btn-lg ms-md-2 text-white" onclick="confirmResetAllData()" title="全データを初期化（リセット）">
          <i class="bi bi-trash-fill"></i> 初期化
        </button>
      </div>
    </div>
  </header>

  <main class="container-fluid py-4 px-md-4" id="exportContent">
    
    <!-- ビュー切り替えタブ & ナビゲーションバー -->
    <div class="card shadow-sm border-0 mb-4 no-print">
      <div class="card-body p-3 bg-white rounded-3">
        <div class="row align-items-center g-3">
          
          <div class="col-xl-5 col-lg-12">
            <ul class="nav nav-pills nav-pills-custom" role="tablist">
              <li class="nav-item">
                <button class="nav-link active" id="tab-daily" onclick="switchMainView('daily')">
                  <i class="bi bi-calendar-day me-1"></i> 日別タイムテーブル
                </button>
              </li>
              <li class="nav-item">
                <button class="nav-link" id="tab-monthly-cal" onclick="switchMainView('monthly-cal')">
                  <i class="bi bi-calendar3 me-1"></i> 月間カレンダー
                </button>
              </li>
              <li class="nav-item">
                <button class="nav-link" id="tab-monthly-fac" onclick="switchMainView('monthly-fac')">
                  <i class="bi bi-grid-3x3 me-1"></i> 月間施設スケジュール
                </button>
              </li>
            </ul>
          </div>

          <div class="col-xl-7 col-lg-12 d-flex justify-content-xl-end align-items-center gap-2 flex-wrap">
            <div class="input-group" style="max-width: 220px;">
              <span class="input-group-text bg-light border-end-0"><i class="bi bi-search text-muted"></i></span>
              <input type="text" id="searchInput" class="form-control border-start-0 ps-0" placeholder="学級/教員/目的で検索" oninput="onSearchInputChanged()">
            </div>

            <div id="dailyNavControls" class="d-flex align-items-center gap-2 flex-wrap">
              <button class="btn btn-outline-primary" onclick="changeDate(-1)">
                <i class="bi bi-chevron-left"></i> 前日
              </button>
              <button class="btn btn-primary fw-bold px-3" onclick="goToToday()">
                今日
              </button>
              <button class="btn btn-outline-primary" onclick="changeDate(1)">
                翌日 <i class="bi bi-chevron-right"></i>
              </button>
              <input type="date" id="datePicker" class="form-control fw-bold border-primary text-primary" style="max-width: 165px;" onchange="onDateSelected(this.value)">
              <span id="dayOfWeekBadge" class="badge fs-6 py-2 px-3"></span>
            </div>

            <div id="monthlyNavControls" class="d-flex align-items-center gap-2 d-none">
              <button class="btn btn-outline-primary" onclick="changeMonth(-1)">
                <i class="bi bi-chevron-left"></i> 前月
              </button>
              <button class="btn btn-primary fw-bold px-3" onclick="goToCurrentMonth()">
                今月
              </button>
              <button class="btn btn-outline-primary" onclick="changeMonth(1)">
                翌月 <i class="bi bi-chevron-right"></i>
              </button>
              <input type="month" id="monthPicker" class="form-control fw-bold border-primary text-primary" style="max-width: 165px;" onchange="onMonthSelected(this.value)">
            </div>
          </div>

        </div>
      </div>
    </div>

    <!-- ビュー1: 日別タイムテーブル -->
    <div id="viewDaily" class="timetable-container p-3 fade-in">
      <div class="table-responsive">
        <table class="table table-bordered table-schedule align-middle">
          <thead>
            <tr id="dailyTableHeaderRow">
              <th class="period-header">校時 \ 施設</th>
            </tr>
          </thead>
          <tbody id="dailyTableBody"></tbody>
        </table>
      </div>
    </div>

    <!-- ビュー2: 月間カレンダー -->
    <div id="viewMonthlyCalendar" class="card shadow-sm border-0 p-4 fade-in d-none">
      <div class="d-flex justify-content-between align-items-center mb-3 flex-wrap gap-2">
        <h3 class="h4 fw-bold text-dark mb-0">
          <i class="bi bi-calendar3 text-primary me-2"></i><span id="monthlyCalTitle"></span> 予約カレンダー
        </h3>
        <div class="d-flex align-items-center gap-3 fs-6 flex-wrap">
          <span class="d-flex align-items-center"><span class="badge bg-primary me-1">土曜</span> 青色</span>
          <span class="d-flex align-items-center"><span class="badge bg-danger me-1">日曜</span> 赤色</span>
          <span class="d-flex align-items-center"><span class="badge bg-danger me-1">㊗️ 祝日</span> ピンク色</span>
          <span class="d-flex align-items-center"><span class="badge me-1" style="background-color: var(--closed-color);">🏢 学校閉庁日</span> 紫色</span>
        </div>
      </div>

      <div class="monthly-calendar-grid mb-2">
        <div class="calendar-day-head sun">日</div>
        <div class="calendar-day-head">月</div>
        <div class="calendar-day-head">火</div>
        <div class="calendar-day-head">水</div>
        <div class="calendar-day-head">木</div>
        <div class="calendar-day-head">金</div>
        <div class="calendar-day-head sat">土</div>
      </div>

      <div id="monthlyCalendarGrid" class="monthly-calendar-grid"></div>
    </div>

    <!-- ビュー3: 月間施設スケジュール -->
    <div id="viewMonthlyFacility" class="timetable-container p-3 fade-in d-none">
      <div class="d-flex flex-wrap justify-content-between align-items-center mb-3 px-2 gap-2">
        <div class="d-flex align-items-center gap-2">
          <label for="selectMonthlyFacility" class="fw-bold fs-5 mb-0 text-dark">表示施設:</label>
          <select id="selectMonthlyFacility" class="form-select form-select-lg border-primary fw-bold" style="min-width: 220px;" onchange="renderMonthlyFacilityTable()"></select>
        </div>
        <div class="text-muted small">
          <i class="bi bi-info-circle me-1"></i> 土日祝日および学校閉庁日の行は色分け表示されます。
        </div>
      </div>

      <div class="table-responsive">
        <table class="table table-bordered table-schedule monthly-facility-table align-middle">
          <thead>
            <tr id="monthlyFacHeaderRow">
              <th class="day-cell-header">日付 \ 校時</th>
            </tr>
          </thead>
          <tbody id="monthlyFacTableBody"></tbody>
        </table>
      </div>
    </div>

  </main>

  <!-- モーダル群 -->
  <div class="modal fade" id="newBookingModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-lg modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header bg-primary text-white">
          <h5 class="modal-title fw-bold"><i class="bi bi-calendar-plus me-2"></i>施設利用の新規予約</h5>
          <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="閉じる"></button>
        </div>
        <div class="modal-body p-4">
          <div id="bookingAlert" class="alert alert-danger d-none fw-bold animate__animated animate__headShake" role="alert">
            <i class="bi bi-exclamation-triangle-fill me-2"></i><span id="bookingAlertMessage"></span>
          </div>

          <form id="bookingForm" onsubmit="saveBooking(event)">
            <div class="row g-3">
              <div class="col-md-6">
                <label for="inputDate" class="form-label fw-bold">利用日 <span class="badge bg-danger">必須</span></label>
                <div class="d-flex align-items-center gap-2">
                  <input type="date" class="form-control form-control-lg" id="inputDate" required onchange="onModalDateChanged(this.value)">
                  <span id="modalDateHolidayBadge"></span>
                </div>
              </div>
              <div class="col-md-6">
                <label for="inputFacility" class="form-label fw-bold">利用施設 <span class="badge bg-danger">必須</span></label>
                <select class="form-select form-select-lg" id="inputFacility" required></select>
              </div>

              <div class="col-12">
                <label class="form-label fw-bold mb-1">対象校時（複数選択可） <span class="badge bg-danger">必須</span></label>
                <div class="d-flex flex-wrap gap-2 p-3 bg-light rounded-3 border" id="periodsContainer"></div>
              </div>

              <div class="col-md-6">
                <label for="inputClassGroup" class="form-label fw-bold">学年・学級 / 団体名 <span class="badge bg-danger">必須</span></label>
                <input type="text" class="form-control form-control-lg" id="inputClassGroup" placeholder="例: 5年1組、科学部、全校朝会" required>
              </div>

              <div class="col-md-6">
                <label for="inputTeacher" class="form-label fw-bold">担当教員名 <span class="badge bg-danger">必須</span></label>
                <input type="text" class="form-control form-control-lg" id="inputTeacher" placeholder="例: 鈴木 太郎" required>
              </div>

              <div class="col-12">
                <label for="inputPurpose" class="form-label fw-bold">利用目的・授業内容 <span class="badge bg-danger">必須</span></label>
                <input type="text" class="form-control form-control-lg" id="inputPurpose" placeholder="例: 理科実験（振り子の運動）、部活練習" required>
              </div>

              <div class="col-12">
                <label for="inputNotes" class="form-label fw-bold">使用備品・配慮事項・メモ（任意）</label>
                <textarea class="form-control" id="inputNotes" rows="2" placeholder="例: プロジェクター使用、準備のため前日放課後セット希望"></textarea>
              </div>
            </div>

            <div class="modal-footer px-0 pb-0 mt-4">
              <button type="button" class="btn btn-secondary btn-lg" data-bs-dismiss="modal">キャンセル</button>
              <button type="submit" class="btn btn-primary btn-lg fw-bold px-4">
                <i class="bi bi-check-circle-fill me-1"></i> 予約を確定する
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="detailBookingModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title fw-bold"><i class="bi bi-info-circle me-2"></i>予約詳細情報</h5>
          <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="閉じる"></button>
        </div>
        <div class="modal-body p-4">
          <input type="hidden" id="detailBookingId">
          <div class="mb-3 d-flex align-items-center justify-content-between flex-wrap gap-2">
            <span id="detailFacilityBadge" class="facility-badge fs-6"></span>
            <span id="detailDateText" class="fw-bold"></span>
          </div>
          
          <div class="card bg-light border-0 mb-3">
            <div class="card-body">
              <h4 id="detailClassGroup" class="card-title fw-bold text-primary mb-2"></h4>
              <p class="card-text mb-1"><i class="bi bi-clock me-2 text-secondary"></i><strong id="detailPeriod"></strong></p>
              <p class="card-text mb-1"><i class="bi bi-person-fill me-2 text-secondary"></i>担当: <strong id="detailTeacher"></strong></p>
              <p class="card-text mb-0"><i class="bi bi-journal-text me-2 text-secondary"></i>目的: <span id="detailPurpose"></span></p>
            </div>
          </div>

          <div id="detailNotesBox" class="p-3 bg-warning-subtle rounded-3 mb-3 border border-warning d-none">
            <h6 class="fw-bold mb-1 text-dark"><i class="bi bi-chat-left-dots-fill me-1"></i> 使用備品・メモ:</h6>
            <p id="detailNotes" class="mb-0 text-dark small"></p>
          </div>

          <div class="d-grid mb-2">
            <button class="btn btn-outline-primary btn-lg fw-bold" onclick="openGoogleCalendarFromDetail()">
              <i class="bi bi-calendar-plus-fill me-2 text-primary"></i> 自分のGoogleカレンダーに追加する
            </button>
          </div>
        </div>
        <div class="modal-footer d-flex justify-content-between">
          <button type="button" class="btn btn-outline-danger fw-bold" onclick="cancelCurrentBooking()">
            <i class="bi bi-trash3-fill me-1"></i> 予約を取り消す
          </button>
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">閉じる</button>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="closedDayManagerModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-lg modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header text-white" style="background-color: var(--closed-color);">
          <h5 class="modal-title fw-bold"><i class="bi bi-building-fill-lock me-2"></i>学校独自の閉庁日・休日設定</h5>
          <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="閉じる"></button>
        </div>
        <div class="modal-body p-4">
          <div class="card bg-light border-0 p-3 mb-4">
            <h6 class="fw-bold text-dark mb-3"><i class="bi bi-plus-circle-fill me-1" style="color: var(--closed-color);"></i>新しい学校閉庁日・休日を追加する</h6>
            <form id="addClosedDayForm" onsubmit="addClosedDay(event)" class="row g-2">
              <div class="col-md-4">
                <input type="date" id="newClosedDate" class="form-control" required>
              </div>
              <div class="col-md-6">
                <input type="text" id="newClosedName" class="form-control" placeholder="例: 夏季学校閉庁日、開校記念日" required>
              </div>
              <div class="col-md-2">
                <button type="submit" class="btn w-100 fw-bold text-white" style="background-color: var(--closed-color);">追加</button>
              </div>
            </form>
          </div>

          <h6 class="fw-bold text-dark mb-2"><i class="bi bi-calendar-event me-1"></i>登録済みの学校閉庁日一覧</h6>
          <div class="table-responsive">
            <table class="table table-bordered align-middle">
              <thead class="table-dark">
                <tr>
                  <th style="width: 25%;">日付</th>
                  <th style="width: 20%;">区分</th>
                  <th style="width: 40%;">名称・理由</th>
                  <th style="width: 15%;" class="text-center">操作</th>
                </tr>
              </thead>
              <tbody id="closedDayListTableBody"></tbody>
            </table>
          </div>
        </div>
        <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">閉じる</button></div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="periodManagerModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-lg modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header bg-warning text-dark">
          <h5 class="modal-title fw-bold"><i class="bi bi-clock-history me-2"></i>学校独自の日課・時間帯設定</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="閉じる"></button>
        </div>
        <div class="modal-body p-4">
          <div class="card bg-light border-0 p-3 mb-4">
            <h6 class="fw-bold text-dark mb-3"><i class="bi bi-plus-circle-fill me-1 text-warning"></i>新しいコマ・時間帯を追加する</h6>
            <form id="addPeriodForm" onsubmit="addPeriod(event)" class="row g-2">
              <div class="col-md-5">
                <input type="text" id="newPeriodName" class="form-control" placeholder="コマ名 (例: 1時間目)" required>
              </div>
              <div class="col-md-3">
                <input type="time" id="newPeriodStartTime" class="form-control" required>
              </div>
              <div class="col-md-3">
                <input type="time" id="newPeriodEndTime" class="form-control" required>
              </div>
              <div class="col-md-1">
                <button type="submit" class="btn btn-warning w-100 fw-bold"><i class="bi bi-plus-lg"></i></button>
              </div>
            </form>
          </div>
          <div class="table-responsive">
            <table class="table table-bordered align-middle mb-0">
              <thead class="table-dark">
                <tr>
                  <th style="width: 12%;" class="text-center">順序</th>
                  <th style="width: 35%;">コマ名</th>
                  <th style="width: 35%;">時間帯</th>
                  <th style="width: 18%;" class="text-center">操作</th>
                </tr>
              </thead>
              <tbody id="periodListTableBody"></tbody>
            </table>
          </div>
        </div>
        <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">閉じる</button></div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="facilityManagerModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-lg modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header bg-secondary text-white">
          <h5 class="modal-title fw-bold"><i class="bi bi-gear-fill me-2"></i>施設・特別教室の登録管理</h5>
          <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="閉じる"></button>
        </div>
        <div class="modal-body p-4">
          <div class="card bg-light border-0 p-3 mb-4">
            <h6 class="fw-bold text-dark mb-3"><i class="bi bi-plus-circle me-1"></i>新しい施設を追加する</h6>
            <form id="addFacilityForm" onsubmit="addFacility(event)" class="row g-2">
              <div class="col-md-5">
                <input type="text" id="newFacilityName" class="form-control" placeholder="施設名（例: 武道場）" required>
              </div>
              <div class="col-md-3">
                <input type="text" id="newFacilityEmoji" class="form-control" placeholder="絵文字 (例: 🥋)" maxlength="2" required>
              </div>
              <div class="col-md-2">
                <input type="color" id="newFacilityColor" class="form-control form-control-color w-100" value="#0d6efd">
              </div>
              <div class="col-md-2">
                <button type="submit" class="btn btn-success w-100 fw-bold">追加</button>
              </div>
            </form>
          </div>
          <div class="table-responsive">
            <table class="table table-bordered align-middle">
              <thead class="table-dark">
                <tr>
                  <th style="width: 15%;">アイコン</th>
                  <th style="width: 40%;">施設名</th>
                  <th style="width: 25%;">カラー</th>
                  <th style="width: 20%;" class="text-center">操作</th>
                </tr>
              </thead>
              <tbody id="facilityListTableBody"></tbody>
            </table>
          </div>
        </div>
        <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">閉じる</button></div>
      </div>
    </div>
  </div>

  <!-- トースト通知 -->
  <div class="toast-container">
    <div id="liveToast" class="toast align-items-center text-white bg-dark border-0 shadow" role="alert" aria-live="assertive" aria-atomic="true">
      <div class="d-flex">
        <div class="toast-body fs-6" id="toastMessage"></div>
        <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="閉じる"></button>
      </div>
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

  <script>
    const STORAGE_KEY_PERIODS = 'school_booking_periods_v5';
    const STORAGE_KEY_FACILITIES = 'school_booking_facilities_v5';
    const STORAGE_KEY_RESERVATIONS = 'school_booking_reservations_v5';
    const STORAGE_KEY_CLOSED_DAYS = 'school_booking_closed_days_v5';

    const DEFAULT_PERIODS = [
      { id: 'p_1', name: '朝の会・朝学習', startTime: '08:25', endTime: '08:40' },
      { id: 'p_2', name: '1時間目', startTime: '08:45', endTime: '09:30' },
      { id: 'p_3', name: '2時間目', startTime: '09:35', endTime: '10:20' },
      { id: 'p_4', name: '3時間目', startTime: '10:40', endTime: '11:25' },
      { id: 'p_5', name: '4時間目', startTime: '11:30', endTime: '12:15' },
      { id: 'p_6', name: '給食・昼休み', startTime: '12:15', endTime: '13:20' },
      { id: 'p_7', name: '5時間目', startTime: '13:25', endTime: '14:10' },
      { id: 'p_8', name: '6時間目', startTime: '14:15', endTime: '15:00' },
      { id: 'p_9', name: '放課後・部活', startTime: '15:05', endTime: '17:00' }
    ];

    const DEFAULT_FACILITIES = [
      { id: 'fac_1', name: '理科室1', emoji: '🧪', color: '#198754' },
      { id: 'fac_2', name: '体育館', emoji: '🏀', color: '#fd7e14' },
      { id: 'fac_3', name: '図書室', emoji: '📚', color: '#20c997' },
      { id: 'fac_4', name: 'パソコン室', emoji: '💻', color: '#0d6efd' },
      { id: 'fac_5', name: '音楽室', emoji: '🎵', color: '#d63384' },
      { id: 'fac_6', name: '家庭科室', emoji: '🍳', color: '#6f42c1' }
    ];

    const DEFAULT_CLOSED_DAYS = [
      { id: 'cd_1', date: '2026-08-11', name: '山の日 / 夏季学校閉庁日' },
      { id: 'cd_2', date: '2026-08-12', name: '夏季学校閉庁日' },
      { id: 'cd_3', date: '2026-08-13', name: '夏季学校閉庁日' },
      { id: 'cd_4', date: '2026-08-14', name: '夏季学校閉庁日' },
      { id: 'cd_5', date: '2026-08-15', name: '夏季学校閉庁日' },
      { id: 'cd_6', date: '2026-10-01', name: '開校記念日' }
    ];

    let currentSelectedDate = getTodayString();
    let currentSelectedMonth = getTodayString().substring(0, 7);
    let activeMainView = 'daily';
    let searchQuery = '';

    let periods = [];
    let facilities = [];
    let reservations = [];
    let schoolClosedDays = [];

    let holidayMap = {};

    let newBookingModalObj, periodManagerModalObj, detailBookingModalObj, facilityManagerModalObj, closedDayManagerModalObj;

    window.addEventListener('DOMContentLoaded', async () => {
      initModals();
      loadPeriods();
      loadFacilities();
      loadReservations();
      loadClosedDays();

      document.getElementById('datePicker').value = currentSelectedDate;
      document.getElementById('monthPicker').value = currentSelectedMonth;

      renderPeriodsCheckbox();
      renderFacilitiesSelect();
      renderMonthlyFacilitySelect();

      updateDateLabel();
      renderActiveView();
      updatePrintHeader();

      await fetchHolidays();
    });

    async function fetchHolidays() {
      try {
        const res = await fetch('https://holidays-jp.github.io/api/v1/date.json');
        if (res.ok) {
          holidayMap = await res.json();
          updateDateLabel();
          renderActiveView();
        }
      } catch (e) {
        console.warn('祝日データ取得失敗:', e);
      }
    }

    function initModals() {
      newBookingModalObj = new bootstrap.Modal(document.getElementById('newBookingModal'));
      periodManagerModalObj = new bootstrap.Modal(document.getElementById('periodManagerModal'));
      detailBookingModalObj = new bootstrap.Modal(document.getElementById('detailBookingModal'));
      facilityManagerModalObj = new bootstrap.Modal(document.getElementById('facilityManagerModal'));
      closedDayManagerModalObj = new bootstrap.Modal(document.getElementById('closedDayManagerModal'));
    }

    function loadPeriods() {
      const data = localStorage.getItem(STORAGE_KEY_PERIODS);
      periods = data ? JSON.parse(data) : JSON.parse(JSON.stringify(DEFAULT_PERIODS));
    }
    function savePeriods() { localStorage.setItem(STORAGE_KEY_PERIODS, JSON.stringify(periods)); }

    function loadFacilities() {
      const data = localStorage.getItem(STORAGE_KEY_FACILITIES);
      facilities = data ? JSON.parse(data) : JSON.parse(JSON.stringify(DEFAULT_FACILITIES));
    }
    function saveFacilities() { localStorage.setItem(STORAGE_KEY_FACILITIES, JSON.stringify(facilities)); }

    function loadReservations() {
      const data = localStorage.getItem(STORAGE_KEY_RESERVATIONS);
      reservations = data ? JSON.parse(data) : [];
    }
    function saveReservations() { localStorage.setItem(STORAGE_KEY_RESERVATIONS, JSON.stringify(reservations)); }

    function loadClosedDays() {
      const data = localStorage.getItem(STORAGE_KEY_CLOSED_DAYS);
      schoolClosedDays = data ? JSON.parse(data) : JSON.parse(JSON.stringify(DEFAULT_CLOSED_DAYS));
    }
    function saveClosedDays() { localStorage.setItem(STORAGE_KEY_CLOSED_DAYS, JSON.stringify(schoolClosedDays)); }

    function getTodayString() {
      const d = new Date();
      return `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}-${String(d.getDate()).padStart(2, '0')}`;
    }

    function getDateInfo(dateStr) {
      if (!dateStr) {
        return { dayOfWeekIdx: 0, isSaturday: false, isSunday: false, isHoliday: false, holidayName: '', isSchoolClosed: false, schoolClosedName: '' };
      }

      const d = new Date(dateStr + 'T00:00:00');
      const dayOfWeekIdx = d.getDay();
      const isSunday = dayOfWeekIdx === 0;
      const isSaturday = dayOfWeekIdx === 6;

      const holidayName = holidayMap[dateStr] || '';
      const isHoliday = !!holidayName;

      const closedObj = schoolClosedDays.find(c => c.date === dateStr);
      const isSchoolClosed = !!closedObj;
      const schoolClosedName = closedObj ? closedObj.name : '';

      return { dayOfWeekIdx, isSaturday, isSunday, isHoliday, holidayName, isSchoolClosed, schoolClosedName };
    }

    async function exportToPDF() {
      showToast('PDFを作成中...');
      
      const element = document.getElementById('exportContent');
      if (!element) return;

      if (typeof html2pdf !== 'undefined') {
        const opt = {
          margin: [8, 8, 8, 8],
          filename: `施設予約表_${currentSelectedDate}.pdf`,
          image: { type: 'jpeg', quality: 0.98 },
          html2canvas: { scale: 2, useCORS: true, allowTaint: true, logging: false, scrollX: 0, scrollY: 0 },
          jsPDF: { unit: 'mm', format: 'a4', orientation: 'landscape' }
        };

        try {
          await html2pdf().set(opt).from(element).save();
          showToast('PDFの保存が完了しました');
          return;
        } catch (err) {
          console.warn('html2pdfで例外発生。印刷ダイアログを使用:', err);
        }
      }

      showToast('印刷画面から「PDFとして保存」を選択してください');
      setTimeout(() => { window.print(); }, 500);
    }

    function openGoogleCalendarFromDetail() {
      const bookingId = document.getElementById('detailBookingId').value;
      const booking = reservations.find(r => r.id === bookingId);
      if (!booking) return;

      const fac = facilities.find(f => f.id === booking.facilityId) || { name: '施設' };
      const p = periods.find(p => p.id === booking.periodId) || { name: 'コマ', startTime: '09:00', endTime: '10:00' };

      const startClean = (p.startTime || '09:00').replace(':', '') + '00';
      const endClean = (p.endTime || '10:00').replace(':', '') + '00';
      const dateClean = booking.date.replace(/-/g, '');

      const startIso = `${dateClean}T${startClean}`;
      const endIso = `${dateClean}T${endClean}`;

      const title = encodeURIComponent(`【${fac.name}】${booking.classGroup} (${booking.teacher})`);
      const details = encodeURIComponent(`目的: ${booking.purpose}\n担当: ${booking.teacher}\nメモ: ${booking.notes || 'なし'}`);
      const location = encodeURIComponent(fac.name);

      const calUrl = `https://calendar.google.com/calendar/render?action=TEMPLATE&text=${title}&dates=${startIso}/${endIso}&details=${details}&location=${location}`;
      window.open(calUrl, '_blank');
    }

    function changeDate(days) {
      const d = new Date(currentSelectedDate + 'T00:00:00');
      d.setDate(d.getDate() + days);
      currentSelectedDate = formatDateStandard(d);
      currentSelectedMonth = currentSelectedDate.substring(0, 7);
      
      document.getElementById('datePicker').value = currentSelectedDate;
      document.getElementById('monthPicker').value = currentSelectedMonth;
      updateDateLabel();
      renderActiveView();
    }

    function goToToday() {
      currentSelectedDate = getTodayString();
      currentSelectedMonth = currentSelectedDate.substring(0, 7);
      document.getElementById('datePicker').value = currentSelectedDate;
      document.getElementById('monthPicker').value = currentSelectedMonth;
      updateDateLabel();
      renderActiveView();
    }

    function onDateSelected(val) {
      if (!val) return;
      currentSelectedDate = val;
      currentSelectedMonth = val.substring(0, 7);
      document.getElementById('monthPicker').value = currentSelectedMonth;
      updateDateLabel();
      renderActiveView();
    }

    function changeMonth(diff) {
      const [y, m] = currentSelectedMonth.split('-').map(Number);
      const d = new Date(y, m - 1 + diff, 1);
      const newY = d.getFullYear();
      const newM = String(d.getMonth() + 1).padStart(2, '0');
      currentSelectedMonth = `${newY}-${newM}`;
      currentSelectedDate = `${currentSelectedMonth}-01`;
      document.getElementById('datePicker').value = currentSelectedDate;
      document.getElementById('monthPicker').value = currentSelectedMonth;
      updateDateLabel();
      renderActiveView();
    }

    function goToCurrentMonth() {
      currentSelectedMonth = getTodayString().substring(0, 7);
      currentSelectedDate = getTodayString();
      document.getElementById('datePicker').value = currentSelectedDate;
      document.getElementById('monthPicker').value = currentSelectedMonth;
      updateDateLabel();
      renderActiveView();
    }

    function onMonthSelected(val) {
      if (!val) return;
      currentSelectedMonth = val;
      currentSelectedDate = `${val}-01`;
      document.getElementById('datePicker').value = currentSelectedDate;
      updateDateLabel();
      renderActiveView();
    }

    function onSearchInputChanged() {
      searchQuery = document.getElementById('searchInput').value.trim().toLowerCase();
      renderActiveView();
    }

    function updateDateLabel() {
      const info = getDateInfo(currentSelectedDate);
      const days = ['日', '月', '火', '水', '木', '金', '土'];
      const dayOfWeekStr = days[info.dayOfWeekIdx];

      const badge = document.getElementById('dayOfWeekBadge');

      if (info.isSchoolClosed) {
        badge.textContent = `🏢 学校閉庁日: ${info.schoolClosedName} (${dayOfWeekStr})`;
        badge.style.backgroundColor = 'var(--closed-color)';
        badge.className = 'badge text-white fs-6 py-2 px-3 fw-bold';
      } else if (info.isHoliday) {
        badge.textContent = `㊗️ ${info.holidayName} (${dayOfWeekStr})`;
        badge.className = 'badge bg-danger fs-6 py-2 px-3 fw-bold';
      } else if (info.isSunday) {
        badge.textContent = `${dayOfWeekStr}曜日 (休日)`;
        badge.className = 'badge bg-danger fs-6 py-2 px-3 fw-bold';
      } else if (info.isSaturday) {
        badge.textContent = `${dayOfWeekStr}曜日 (土曜)`;
        badge.className = 'badge bg-primary fs-6 py-2 px-3 fw-bold';
      } else {
        badge.textContent = `${dayOfWeekStr}曜日`;
        badge.className = 'badge bg-secondary fs-6 py-2 px-3';
      }

      updatePrintHeader();
    }

    function updatePrintHeader() {
      const info = getDateInfo(currentSelectedDate);
      let daySuffix = '';
      if (info.isSchoolClosed) daySuffix = ` [🏢 学校閉庁日: ${info.schoolClosedName}]`;
      else if (info.isHoliday) daySuffix = ` [㊗️ ${info.holidayName}]`;

      if (activeMainView === 'daily') {
        document.getElementById('printSubTitle').textContent = `対象日: ${formatDateJapanese(currentSelectedDate)}${daySuffix}`;
      } else {
        document.getElementById('printSubTitle').textContent = `対象月: ${currentSelectedMonth.replace('-', '年')}月`;
      }
    }

    function formatDateStandard(dateObj) {
      const y = dateObj.getFullYear();
      const m = String(dateObj.getMonth() + 1).padStart(2, '0');
      const d = String(dateObj.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    }

    function formatDateJapanese(dateStr) {
      if (!dateStr) return '';
      const [y, m, d] = dateStr.split('-');
      return `${y}年${parseInt(m, 10)}月${parseInt(d, 10)}日`;
    }

    function isBookingMatchSearch(booking) {
      if (!searchQuery) return true;
      const q = searchQuery;
      return (
        booking.classGroup.toLowerCase().includes(q) ||
        booking.teacher.toLowerCase().includes(q) ||
        booking.purpose.toLowerCase().includes(q) ||
        (booking.notes && booking.notes.toLowerCase().includes(q))
      );
    }

    function switchMainView(viewName) {
      activeMainView = viewName;

      document.getElementById('tab-daily').classList.toggle('active', viewName === 'daily');
      document.getElementById('tab-monthly-cal').classList.toggle('active', viewName === 'monthly-cal');
      document.getElementById('tab-monthly-fac').classList.toggle('active', viewName === 'monthly-fac');

      document.getElementById('dailyNavControls').classList.toggle('d-none', viewName !== 'daily');
      document.getElementById('monthlyNavControls').classList.toggle('d-none', viewName === 'daily');

      document.getElementById('viewDaily').classList.toggle('d-none', viewName !== 'daily');
      document.getElementById('viewMonthlyCalendar').classList.toggle('d-none', viewName !== 'monthly-cal');
      document.getElementById('viewMonthlyFacility').classList.toggle('d-none', viewName !== 'monthly-fac');

      renderActiveView();
    }

    function renderActiveView() {
      if (activeMainView === 'daily') renderDailyTable();
      else if (activeMainView === 'monthly-cal') renderMonthlyCalendarGrid();
      else if (activeMainView === 'monthly-fac') renderMonthlyFacilityTable();
    }

    /* 1. 日別タイムテーブル描画 */
    function renderDailyTable() {
      const headerRow = document.getElementById('dailyTableHeaderRow');
      const tbody = document.getElementById('dailyTableBody');

      headerRow.innerHTML = '<th class="period-header">時間（校時） \\ 施設</th>';
      facilities.forEach(fac => {
        const th = document.createElement('th');
        th.className = 'facility-header';
        th.style.borderTop = `4px solid ${fac.color}`;
        th.innerHTML = `${fac.emoji} ${escapeHTML(fac.name)}`;
        headerRow.appendChild(th);
      });

      tbody.innerHTML = '';
      if (periods.length === 0) {
        tbody.innerHTML = `<tr><td colspan="${facilities.length + 1}" class="text-center py-4 text-muted">時限が設定されていません。</td></tr>`;
        return;
      }

      periods.forEach(p => {
        const tr = document.createElement('tr');
        const thPeriod = document.createElement('th');
        thPeriod.className = 'period-header';
        thPeriod.innerHTML = `${escapeHTML(p.name)} ${p.startTime ? `<span class="period-time-text">${p.startTime}~${p.endTime}</span>` : ''}`;
        tr.appendChild(thPeriod);

        facilities.forEach(fac => {
          const td = document.createElement('td');
          const booking = reservations.find(r => r.date === currentSelectedDate && r.facilityId === fac.id && r.periodId === p.id);

          if (booking) {
            const isMatch = isBookingMatchSearch(booking);
            const card = document.createElement('div');
            card.className = `booking-card fade-in ${!isMatch ? 'opacity-25' : ''}`;
            card.style.borderLeftColor = fac.color;
            card.onclick = () => openBookingDetail(booking.id);
            card.innerHTML = `
              <div>
                <div class="class-badge" title="${escapeHTML(booking.classGroup)}">${escapeHTML(booking.classGroup)}</div>
                <div class="teacher-name"><i class="bi bi-person"></i> ${escapeHTML(booking.teacher)}</div>
              </div>
              <div class="purpose-text" title="${escapeHTML(booking.purpose)}">${escapeHTML(booking.purpose)}</div>
            `;
            td.appendChild(card);
          } else {
            const btn = document.createElement('button');
            btn.type = 'button';
            btn.className = 'empty-slot-btn no-print';
            btn.innerHTML = '<i class="bi bi-plus-lg fs-5"></i> 予約';
            btn.onclick = () => openNewBookingModal(currentSelectedDate, fac.id, p.id);
            td.appendChild(btn);
          }

          tr.appendChild(td);
        });

        tbody.appendChild(tr);
      });
    }

    /* 2. 月間カレンダーグリッド描画 */
    function renderMonthlyCalendarGrid() {
      const [yearStr, monthStr] = currentSelectedMonth.split('-');
      const year = parseInt(yearStr, 10);
      const month = parseInt(monthStr, 10);

      document.getElementById('monthlyCalTitle').textContent = `${year}年${month}月`;

      const gridContainer = document.getElementById('monthlyCalendarGrid');
      gridContainer.innerHTML = '';

      const firstDay = new Date(year, month - 1, 1);
      const lastDay = new Date(year, month, 0);
      
      const startDayOfWeek = firstDay.getDay();
      const totalDays = lastDay.getDate();

      const todayStr = getTodayString();

      const prevMonthLastDay = new Date(year, month - 1, 0).getDate();
      for (let i = startDayOfWeek - 1; i >= 0; i--) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day-cell other-month';
        cell.innerHTML = `<div class="calendar-date-number">${prevMonthLastDay - i}</div>`;
        gridContainer.appendChild(cell);
      }

      for (let day = 1; day <= totalDays; day++) {
        const dayStr = String(day).padStart(2, '0');
        const dateStr = `${currentSelectedMonth}-${dayStr}`;
        const dateInfo = getDateInfo(dateStr);

        const cell = document.createElement('div');
        let cellClass = 'calendar-day-cell';

        if (dateInfo.isSchoolClosed) cellClass += ' is-closed';
        else if (dateInfo.isHoliday) cellClass += ' is-holiday';
        else if (dateInfo.isSunday) cellClass += ' is-sunday';
        else if (dateInfo.isSaturday) cellClass += ' is-saturday';

        if (dateStr === todayStr) cellClass += ' is-today';
        cell.className = cellClass;

        cell.onclick = () => {
          currentSelectedDate = dateStr;
          document.getElementById('datePicker').value = dateStr;
          updateDateLabel();
          switchMainView('daily');
        };

        const dayReservations = reservations.filter(r => r.date === dateStr && isBookingMatchSearch(r));

        let dateNumColor = '';
        if (dateInfo.isSchoolClosed) dateNumColor = 'text-purple';
        else if (dateInfo.isHoliday || dateInfo.isSunday) dateNumColor = 'text-danger';
        else if (dateInfo.isSaturday) dateNumColor = 'text-primary';

        let tagHtml = '';
        if (dateInfo.isSchoolClosed) {
          tagHtml = `<span class="closed-day-tag" title="学校閉庁日: ${escapeHTML(dateInfo.schoolClosedName)}">🏢 ${escapeHTML(dateInfo.schoolClosedName)}</span>`;
        } else if (dateInfo.isHoliday) {
          tagHtml = `<span class="holiday-tag" title="${escapeHTML(dateInfo.holidayName)}">㊗️ ${escapeHTML(dateInfo.holidayName)}</span>`;
        }

        let html = `
          <div class="calendar-date-number ${dateNumColor}">
            <span>${day}</span>
            ${tagHtml}
            ${dayReservations.length > 0 ? `<span class="badge rounded-pill bg-primary ms-auto" style="font-size:0.75rem;">${dayReservations.length}件</span>` : ''}
          </div>
          <div class="flex-grow-1 overflow-hidden">
        `;

        const displayList = dayReservations.slice(0, 3);
        displayList.forEach(r => {
          const fac = facilities.find(f => f.id === r.facilityId) || { name: '施設', color: '#6c757d', emoji: '🏫' };
          const p = periods.find(p => p.id === r.periodId) || { name: '' };
          html += `
            <div class="calendar-booking-badge" style="background-color: ${fac.color}" title="${fac.name} (${p.name}): ${r.classGroup}">
              ${fac.emoji} ${escapeHTML(r.classGroup)} (${escapeHTML(p.name)})
            </div>
          `;
        });

        if (dayReservations.length > 3) {
          html += `<div class="text-muted small text-center fw-bold">+ 他 ${dayReservations.length - 3} 件</div>`;
        }

        html += `</div>`;
        cell.innerHTML = html;
        gridContainer.appendChild(cell);
      }

      const totalRendered = startDayOfWeek + totalDays;
      const nextMonthDays = (totalRendered % 7 === 0) ? 0 : 7 - (totalRendered % 7);
      for (let i = 1; i <= nextMonthDays; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day-cell other-month';
        cell.innerHTML = `<div class="calendar-date-number">${i}</div>`;
        gridContainer.appendChild(cell);
      }
    }

    function renderMonthlyFacilitySelect() {
      const select = document.getElementById('selectMonthlyFacility');
      select.innerHTML = '';
      facilities.forEach(fac => {
        const opt = document.createElement('option');
        opt.value = fac.id;
        opt.textContent = `${fac.emoji} ${fac.name}`;
        select.appendChild(opt);
      });
    }

    function renderMonthlyFacilityTable() {
      const facilityId = document.getElementById('selectMonthlyFacility').value || (facilities[0] ? facilities[0].id : null);
      if (!facilityId) return;

      const targetFac = facilities.find(f => f.id === facilityId) || facilities[0];

      const [yearStr, monthStr] = currentSelectedMonth.split('-');
      const year = parseInt(yearStr, 10);
      const month = parseInt(monthStr, 10);
      const lastDay = new Date(year, month, 0).getDate();

      const headerRow = document.getElementById('monthlyFacHeaderRow');
      const tbody = document.getElementById('monthlyFacTableBody');

      headerRow.innerHTML = `<th class="day-cell-header" style="border-top: 4px solid ${targetFac.color}">${targetFac.emoji} 日付 \\ 校時</th>`;
      periods.forEach(p => {
        const th = document.createElement('th');
        th.innerHTML = `${escapeHTML(p.name)} ${p.startTime ? `<span class="period-time-text">${p.startTime}~${p.endTime}</span>` : ''}`;
        headerRow.appendChild(th);
      });

      tbody.innerHTML = '';

      for (let day = 1; day <= lastDay; day++) {
        const dayStr = String(day).padStart(2, '0');
        const dateStr = `${currentSelectedMonth}-${dayStr}`;
        const dateInfo = getDateInfo(dateStr);
        const daysJa = ['日', '月', '火', '水', '木', '金', '土'];

        const tr = document.createElement('tr');
        const thDay = document.createElement('th');
        let dayHeaderClass = 'day-cell-header';

        if (dateInfo.isSchoolClosed) dayHeaderClass += ' is-school-closed';
        else if (dateInfo.isHoliday) dayHeaderClass += ' is-national-holiday';
        else if (dateInfo.isSunday) dayHeaderClass += ' is-weekend-sun';
        else if (dateInfo.isSaturday) dayHeaderClass += ' is-weekend-sat';

        thDay.className = dayHeaderClass;
        
        let tagHtml = '';
        if (dateInfo.isSchoolClosed) {
          tagHtml = `<div class="closed-day-tag mt-1">🏢 ${escapeHTML(dateInfo.schoolClosedName)}</div>`;
        } else if (dateInfo.isHoliday) {
          tagHtml = `<div class="holiday-tag mt-1">㊗️ ${escapeHTML(dateInfo.holidayName)}</div>`;
        }

        thDay.innerHTML = `${day}日 (${daysJa[dateInfo.dayOfWeekIdx]})${tagHtml}`;
        tr.appendChild(thDay);

        periods.forEach(p => {
          const td = document.createElement('td');
          const booking = reservations.find(r => r.date === dateStr && r.facilityId === targetFac.id && r.periodId === p.id);

          if (booking) {
            const isMatch = isBookingMatchSearch(booking);
            const card = document.createElement('div');
            card.className = `booking-card fade-in ${!isMatch ? 'opacity-25' : ''}`;
            card.style.borderLeftColor = targetFac.color;
            card.onclick = () => openBookingDetail(booking.id);
            card.innerHTML = `
              <div>
                <div class="class-badge" title="${escapeHTML(booking.classGroup)}">${escapeHTML(booking.classGroup)}</div>
                <div class="teacher-name"><i class="bi bi-person"></i> ${escapeHTML(booking.teacher)}</div>
              </div>
              <div class="purpose-text" title="${escapeHTML(booking.purpose)}">${escapeHTML(booking.purpose)}</div>
            `;
            td.appendChild(card);
          } else {
            const btn = document.createElement('button');
            btn.type = 'button';
            btn.className = 'empty-slot-btn no-print';
            btn.innerHTML = '<i class="bi bi-plus-lg"></i>';
            btn.onclick = () => openNewBookingModal(dateStr, targetFac.id, p.id);
            td.appendChild(btn);
          }

          tr.appendChild(td);
        });

        tbody.appendChild(tr);
      }
    }

    function renderFacilitiesSelect() {
      const select = document.getElementById('inputFacility');
      select.innerHTML = '';
      facilities.forEach(fac => {
        const opt = document.createElement('option');
        opt.value = fac.id;
        opt.textContent = `${fac.emoji} ${fac.name}`;
        select.appendChild(opt);
      });
    }

    function renderPeriodsCheckbox() {
      const container = document.getElementById('periodsContainer');
      container.innerHTML = '';
      if (periods.length === 0) {
        container.innerHTML = '<span class="text-danger">時限が設定されていません。</span>';
        return;
      }

      periods.forEach(p => {
        const div = document.createElement('div');
        div.className = 'form-check form-check-inline me-3 mb-2';
        div.innerHTML = `
          <input class="form-check-input period-checkbox" type="checkbox" id="period_check_${p.id}" value="${p.id}">
          <label class="form-check-label fw-semibold" for="period_check_${p.id}">
            ${escapeHTML(p.name)} ${p.startTime ? `<small class="text-muted">(${p.startTime}~${p.endTime})</small>` : ''}
          </label>
        `;
        container.appendChild(div);
      });
    }

    function openNewBookingModal(targetDate = null, targetFacilityId = null, targetPeriodId = null) {
      document.getElementById('bookingForm').reset();
      hideBookingAlert();

      const selectedDate = targetDate || currentSelectedDate;
      document.getElementById('inputDate').value = selectedDate;
      onModalDateChanged(selectedDate);

      renderFacilitiesSelect();
      if (targetFacilityId) document.getElementById('inputFacility').value = targetFacilityId;

      document.querySelectorAll('.period-checkbox').forEach(cb => cb.checked = false);
      if (targetPeriodId) {
        document.querySelectorAll('.period-checkbox').forEach(cb => {
          if (cb.value === targetPeriodId) cb.checked = true;
        });
      }

      newBookingModalObj.show();
    }

    function onModalDateChanged(val) {
      const badgeContainer = document.getElementById('modalDateHolidayBadge');
      if (!val) {
        badgeContainer.innerHTML = '';
        hideBookingAlert();
        return;
      }

      const info = getDateInfo(val);

      if (info.isSchoolClosed) {
        badgeContainer.innerHTML = `<span class="badge text-white fs-6" style="background-color: var(--closed-color);">🏢 学校閉庁日: ${escapeHTML(info.schoolClosedName)}</span>`;
        showBookingAlert(`⚠️ 選択された日付は『学校閉庁日: ${escapeHTML(info.schoolClosedName)}』です。利用にはご注意ください。`);
      } else {
        hideBookingAlert();
        if (info.isHoliday) badgeContainer.innerHTML = `<span class="badge bg-danger fs-6">㊗️ ${escapeHTML(info.holidayName)}</span>`;
        else if (info.isSunday) badgeContainer.innerHTML = `<span class="badge bg-danger fs-6">日曜日</span>`;
        else if (info.isSaturday) badgeContainer.innerHTML = `<span class="badge bg-primary fs-6">土曜日</span>`;
        else badgeContainer.innerHTML = '';
      }
    }

    function saveBooking(e) {
      e.preventDefault();

      const date = document.getElementById('inputDate').value;
      const facilityId = document.getElementById('inputFacility').value;
      const classGroup = document.getElementById('inputClassGroup').value.trim();
      const teacher = document.getElementById('inputTeacher').value.trim();
      const purpose = document.getElementById('inputPurpose').value.trim();
      const notes = document.getElementById('inputNotes').value.trim();

      const selectedPeriodIds = [];
      document.querySelectorAll('.period-checkbox:checked').forEach(cb => selectedPeriodIds.push(cb.value));

      if (selectedPeriodIds.length === 0) {
        showBookingAlert('対象の校時（コマ）を1つ以上選択してください。');
        return;
      }

      const conflicts = [];
      selectedPeriodIds.forEach(pId => {
        const exist = reservations.find(r => r.date === date && r.facilityId === facilityId && r.periodId === pId);
        if (exist) {
          const pName = periods.find(p => p.id === pId)?.name || '選択時限';
          conflicts.push(`【${pName}】 (${exist.teacher} 先生 - ${exist.classGroup})`);
        }
      });

      if (conflicts.length > 0) {
        const facName = facilities.find(f => f.id === facilityId)?.name || '選択施設';
        showBookingAlert(`${facName} の以下の時間帯はすでに予約されています。<br>・${conflicts.join('<br>・')}`);
        return;
      }

      selectedPeriodIds.forEach(pId => {
        const newBooking = {
          id: 'res_' + Date.now() + '_' + Math.random().toString(36).substr(2, 5),
          date: date,
          facilityId: facilityId,
          periodId: pId,
          classGroup: classGroup,
          teacher: teacher,
          purpose: purpose,
          notes: notes,
          createdAt: new Date().toISOString()
        };
        reservations.push(newBooking);
      });

      saveReservations();
      newBookingModalObj.hide();

      renderActiveView();
      showToast(`予約を登録しました（${selectedPeriodIds.length}コマ）`);
    }

    function showBookingAlert(msg) {
      const alertBox = document.getElementById('bookingAlert');
      document.getElementById('bookingAlertMessage').innerHTML = msg;
      alertBox.classList.remove('d-none');
    }

    function hideBookingAlert() {
      document.getElementById('bookingAlert').classList.add('d-none');
    }

    function openBookingDetail(bookingId) {
      const booking = reservations.find(r => r.id === bookingId);
      if (!booking) return;

      const fac = facilities.find(f => f.id === booking.facilityId) || { name: '施設', emoji: '🏫', color: '#6c757d' };
      const p = periods.find(p => p.id === booking.periodId) || { name: 'コマ' };
      const dateInfo = getDateInfo(booking.date);

      document.getElementById('detailBookingId').value = booking.id;

      const badge = document.getElementById('detailFacilityBadge');
      badge.textContent = `${fac.emoji} ${fac.name}`;
      badge.style.backgroundColor = fac.color;

      let dateSuffix = '';
      if (dateInfo.isSchoolClosed) dateSuffix = ` <span class="badge text-white" style="background-color: var(--closed-color);">🏢 学校閉庁日: ${escapeHTML(dateInfo.schoolClosedName)}</span>`;
      else if (dateInfo.isHoliday) dateSuffix = ` <span class="badge bg-danger">㊗️ ${escapeHTML(dateInfo.holidayName)}</span>`;
      else if (dateInfo.isSunday) dateSuffix = ` <span class="badge bg-danger">日曜日</span>`;
      else if (dateInfo.isSaturday) dateSuffix = ` <span class="badge bg-primary">土曜日</span>`;

      document.getElementById('detailDateText').innerHTML = `${formatDateJapanese(booking.date)}${dateSuffix}`;
      document.getElementById('detailClassGroup').textContent = booking.classGroup;
      document.getElementById('detailPeriod').textContent = `${p.name} ${p.startTime ? `(${p.startTime}~${p.endTime})` : ''}`;
      document.getElementById('detailTeacher').textContent = booking.teacher;
      document.getElementById('detailPurpose').textContent = booking.purpose;

      const notesBox = document.getElementById('detailNotesBox');
      if (booking.notes) {
        document.getElementById('detailNotes').textContent = booking.notes;
        notesBox.classList.remove('d-none');
      } else {
        notesBox.classList.add('d-none');
      }

      detailBookingModalObj.show();
    }

    function cancelCurrentBooking() {
      const id = document.getElementById('detailBookingId').value;
      if (!id) return;

      if (confirm('この予約を取り消して削除しますか？')) {
        reservations = reservations.filter(r => r.id !== id);
        saveReservations();
        detailBookingModalObj.hide();
        renderActiveView();
        showToast('予約を取り消しました');
      }
    }

    function openClosedDayManagerModal() {
      renderClosedDayListTable();
      closedDayManagerModalObj.show();
    }

    function renderClosedDayListTable() {
      const tbody = document.getElementById('closedDayListTableBody');
      tbody.innerHTML = '';

      if (schoolClosedDays.length === 0) {
        tbody.innerHTML = '<tr><td colspan="4" class="text-center text-muted py-3">登録された学校閉庁日はありません。</td></tr>';
        return;
      }

      schoolClosedDays.sort((a, b) => a.date.localeCompare(b.date));

      schoolClosedDays.forEach(item => {
        const info = getDateInfo(item.date);
        const daysJa = ['日', '月', '火', '水', '木', '金', '土'];

        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="fw-bold">${item.date} (${daysJa[info.dayOfWeekIdx]})</td>
          <td>${info.isHoliday ? `<span class="badge bg-danger">祝日</span>` : info.isSunday ? `<span class="badge bg-danger">日曜</span>` : info.isSaturday ? `<span class="badge bg-primary">土曜</span>` : `<span class="badge bg-secondary">平日</span>`}</td>
          <td class="fw-bold" style="color: var(--closed-color);">${escapeHTML(item.name)}</td>
          <td class="text-center">
            <button class="btn btn-sm btn-outline-danger" onclick="deleteClosedDay('${item.id}')"><i class="bi bi-trash-fill"></i> 削除</button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function addClosedDay(e) {
      e.preventDefault();
      const date = document.getElementById('newClosedDate').value;
      const name = document.getElementById('newClosedName').value.trim();

      if (!date || !name) return;

      schoolClosedDays.push({ id: 'cd_' + Date.now(), date: date, name: name });
      saveClosedDays();
      document.getElementById('addClosedDayForm').reset();
      renderClosedDayListTable();
      updateDateLabel();
      renderActiveView();
      showToast(`閉庁日「${name}」を登録しました`);
    }

    function deleteClosedDay(id) {
      if (confirm('削除しますか？')) {
        schoolClosedDays = schoolClosedDays.filter(c => c.id !== id);
        saveClosedDays();
        renderClosedDayListTable();
        updateDateLabel();
        renderActiveView();
        showToast('削除しました');
      }
    }

    function openPeriodManagerModal() {
      renderPeriodListTable();
      periodManagerModalObj.show();
    }

    function renderPeriodListTable() {
      const tbody = document.getElementById('periodListTableBody');
      tbody.innerHTML = '';

      periods.forEach((p, index) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="text-center">
            <button class="btn btn-sm btn-light border p-1" onclick="movePeriodOrder(${index}, -1)" ${index === 0 ? 'disabled' : ''}><i class="bi bi-arrow-up"></i></button>
            <button class="btn btn-sm btn-light border p-1" onclick="movePeriodOrder(${index}, 1)" ${index === periods.length - 1 ? 'disabled' : ''}><i class="bi bi-arrow-down"></i></button>
          </td>
          <td><input type="text" class="form-control form-control-sm fw-bold" value="${escapeHTML(p.name)}" onchange="updatePeriodDetail('${p.id}', 'name', this.value)"></td>
          <td>
            <div class="d-flex align-items-center gap-1">
              <input type="time" class="form-control form-control-sm" value="${p.startTime || ''}" onchange="updatePeriodDetail('${p.id}', 'startTime', this.value)">
              <span>〜</span>
              <input type="time" class="form-control form-control-sm" value="${p.endTime || ''}" onchange="updatePeriodDetail('${p.id}', 'endTime', this.value)">
            </div>
          </td>
          <td class="text-center"><button class="btn btn-sm btn-outline-danger" onclick="deletePeriod('${p.id}')"><i class="bi bi-trash-fill"></i></button></td>
        `;
        tbody.appendChild(tr);
      });
    }

    function addPeriod(e) {
      e.preventDefault();
      const name = document.getElementById('newPeriodName').value.trim();
      const startTime = document.getElementById('newPeriodStartTime').value;
      const endTime = document.getElementById('newPeriodEndTime').value;

      if (!name) return;
      periods.push({ id: 'p_' + Date.now(), name: name, startTime: startTime, endTime: endTime });
      savePeriods();
      document.getElementById('addPeriodForm').reset();
      renderPeriodListTable();
      renderPeriodsCheckbox();
      renderActiveView();
      showToast(`コマ「${name}」を追加しました`);
    }

    function updatePeriodDetail(id, key, value) {
      const p = periods.find(item => item.id === id);
      if (!p) return;
      p[key] = value.trim();
      savePeriods();
      renderPeriodsCheckbox();
      renderActiveView();
    }

    function movePeriodOrder(index, direction) {
      const targetIndex = index + direction;
      if (targetIndex < 0 || targetIndex >= periods.length) return;
      const temp = periods[index];
      periods[index] = periods[targetIndex];
      periods[targetIndex] = temp;
      savePeriods();
      renderPeriodListTable();
      renderPeriodsCheckbox();
      renderActiveView();
    }

    function deletePeriod(id) {
      if (confirm('削除してもよろしいですか？')) {
        periods = periods.filter(item => item.id !== id);
        reservations = reservations.filter(r => r.periodId !== id);
        savePeriods();
        saveReservations();
        renderPeriodListTable();
        renderPeriodsCheckbox();
        renderActiveView();
        showToast('削除しました');
      }
    }

    function openFacilityManagerModal() {
      renderFacilityListTable();
      facilityManagerModalObj.show();
    }

    function renderFacilityListTable() {
      const tbody = document.getElementById('facilityListTableBody');
      tbody.innerHTML = '';

      facilities.forEach(fac => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="text-center fs-4">${fac.emoji}</td>
          <td class="fw-bold">${escapeHTML(fac.name)}</td>
          <td><span class="d-inline-block rounded-circle" style="width: 20px; height: 20px; background-color: ${fac.color};"></span></td>
          <td class="text-center"><button class="btn btn-sm btn-outline-danger" onclick="deleteFacility('${fac.id}')"><i class="bi bi-trash-fill"></i> 削除</button></td>
        `;
        tbody.appendChild(tr);
      });
    }

    function addFacility(e) {
      e.preventDefault();
      const name = document.getElementById('newFacilityName').value.trim();
      const emoji = document.getElementById('newFacilityEmoji').value.trim() || '🏫';
      const color = document.getElementById('newFacilityColor').value;

      if (!name) return;
      facilities.push({ id: 'fac_' + Date.now(), name: name, emoji: emoji, color: color });
      saveFacilities();
      document.getElementById('addFacilityForm').reset();
      renderFacilityListTable();
      renderFacilitiesSelect();
      renderMonthlyFacilitySelect();
      renderActiveView();
      showToast(`施設「${name}」を追加しました`);
    }

    function deleteFacility(id) {
      if (facilities.length <= 1) {
        alert('少なくとも1つの施設が必要です。');
        return;
      }
      if (confirm('削除 meよろしいですか？')) {
        facilities = facilities.filter(f => f.id !== id);
        reservations = reservations.filter(r => r.facilityId !== id);
        saveFacilities();
        saveReservations();
        renderFacilityListTable();
        renderFacilitiesSelect();
        renderMonthlyFacilitySelect();
        renderActiveView();
        showToast('削除しました');
      }
    }

    function exportToCSV() {
      if (reservations.length === 0) {
        alert('予約データがありません。');
        return;
      }

      let csv = '\uFEFF予約ID,利用日,休日区分,施設名,校時名,開始時間,終了時間,学年学級/団体,担当教員,利用目的,備考\n';
      reservations.forEach(r => {
        const fac = facilities.find(f => f.id === r.facilityId)?.name || '不明';
        const p = periods.find(p => p.id === r.periodId) || { name: '', startTime: '', endTime: '' };
        const info = getDateInfo(r.date);
        
        let holidayStr = '平日';
        if (info.isSchoolClosed) holidayStr = `学校閉庁日 (${info.schoolClosedName})`;
        else if (info.isHoliday) holidayStr = `祝日 (${info.holidayName})`;
        else if (info.isSunday) holidayStr = '日曜日';
        else if (info.isSaturday) holidayStr = '土曜日';

        const row = [r.id, r.date, holidayStr, fac, p.name, p.startTime, p.endTime, r.classGroup, r.teacher, r.purpose, r.notes || ''];
        csv += row.map(v => `"${String(v).replace(/"/g, '""')}"`).join(',') + '\n';
      });

      const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = `学校施設予約一覧_${getTodayString()}.csv`;
      link.click();
      showToast('CSV（Excel用データ）を出力しました');
    }

    function confirmResetAllData() {
      if (prompt('全データ初期化のため「リセット」と入力してください。') === 'リセット') {
        localStorage.clear();
        periods = JSON.parse(JSON.stringify(DEFAULT_PERIODS));
        facilities = JSON.parse(JSON.stringify(DEFAULT_FACILITIES));
        schoolClosedDays = JSON.parse(JSON.stringify(DEFAULT_CLOSED_DAYS));
        reservations = [];

        savePeriods();
        saveFacilities();
        saveClosedDays();
        saveReservations();

        renderPeriodsCheckbox();
        renderFacilitiesSelect();
        renderMonthlyFacilitySelect();
        updateDateLabel();
        renderActiveView();
        showToast('全データを初期化しました');
      }
    }

    function showToast(msg) {
      document.getElementById('toastMessage').textContent = msg;
      new bootstrap.Toast(document.getElementById('liveToast'), { delay: 2500 }).show();
    }

    function escapeHTML(str) {
      if (!str) return '';
      return String(str).replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;').replace(/'/g, '&#039;');
    }
  </script>
</body>
</html>
