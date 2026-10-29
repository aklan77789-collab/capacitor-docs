```react
import React, { useState, useMemo } from 'react';
import {
  Activity, Users, Calendar, DollarSign, FileText, UserCheck, 
  TrendingUp, TrendingDown, Plus, Search, CheckCircle, Clock, 
  XCircle, AlertCircle, Edit, Trash2, Filter, ChevronRight,
  Shield, Stethoscope, Dumbbell, Zap, HeartPulse, Settings, 
  Award, ArrowUpRight, ArrowDownRight, Menu, X, Check, PieChart,
  CalendarCheck, UserPlus, FileSpreadsheet, CreditCard, Building2,
  Briefcase, Sparkles, User, Percent, Flame, Smartphone, Download,
  ExternalLink, Layers
} from 'lucide-react';

// === البيانات الافتراضية الأولية لمركز كيروان ===

const INITIAL_PATIENTS = [
  {
    id: 'P-101',
    name: 'أحمد محمود السقاف',
    phone: '0501234567',
    age: 42,
    gender: 'ذكر',
    diagnosis: 'انزلاق غضروفي قطني (L4-L5)',
    planType: '10_days', // daily, 10_days, monthly, custom
    planTitle: 'باقة التأهيل المكثف (10 أيام)',
    totalSessions: 10,
    completedSessions: 7,
    startDate: '2026-10-20',
    endDate: '2026-10-30',
    paidAmount: 1200,
    remainingAmount: 0,
    status: 'active',
    improvementRate: 75,
    devicesUsed: ['شد الفقرات الرقمي', 'التنبيه الكهربائي TENS', 'الموجات الصوتية US'],
    therapist: 'د. خالد العمري',
    notes: 'تحسن ملحوظ في مدى الحركة، انخفاض حدة الألم من 8/10 إلى 3/10.'
  },
  {
    id: 'P-102',
    name: 'سارة عبد الله الحسين',
    phone: '0559876543',
    age: 28,
    gender: 'أنثى',
    diagnosis: 'إعادة تأهيل بعد جراحة الرباط الصليبي (ACL)',
    planType: 'monthly',
    planTitle: 'اشتراك شهري شامل',
    totalSessions: 24,
    completedSessions: 18,
    startDate: '2026-10-01',
    endDate: '2026-10-31',
    paidAmount: 2500,
    remainingAmount: 500,
    status: 'active',
    improvementRate: 85,
    devicesUsed: ['تمارين التوازن والتأهيل', 'الموجات الصدمية Shockwave', 'الكمادات الثلجية'],
    therapist: 'د. منيرة الفاضل',
    notes: 'تم البدء بتمارين التقوية المتقدمة للجزء الأمامي للفخذ.'
  },
  {
    id: 'P-103',
    name: 'عمر فاروق التميمي',
    phone: '0541122334',
    age: 65,
    gender: 'ذكر',
    diagnosis: 'خشونة مفصل الركبة من الدرجة الثالثة',
    planType: 'daily',
    planTitle: 'جلسة فردية يومية',
    totalSessions: 1,
    completedSessions: 1,
    startDate: '2026-10-29',
    endDate: '2026-10-29',
    paidAmount: 150,
    remainingAmount: 0,
    status: 'active',
    improvementRate: 40,
    devicesUsed: ['Laser العلاجي', 'التدليك العلاجي'],
    therapist: 'د. خالد العمري',
    notes: 'يحتاج إلى مواظبة على التمارين المنزلية.'
  },
  {
    id: 'P-104',
    name: 'فاطمة محمد الزهراني',
    phone: '0567788990',
    age: 9,
    gender: 'أنثى',
    diagnosis: 'تأهيل شلل دماغي (CP) - شلل سفلي تشنجي',
    planType: 'monthly',
    planTitle: 'اشتراك شهري تأهيل أطفال',
    totalSessions: 20,
    completedSessions: 19,
    startDate: '2026-10-01',
    endDate: '2026-10-30',
    paidAmount: 2200,
    remainingAmount: 0,
    status: 'expiring_soon',
    improvementRate: 60,
    devicesUsed: ['تمارين الإطالة والتوازن', 'العلاج الوظيفي'],
    therapist: 'د. ريم السالم',
    notes: 'باقي جلسة واحدة وينتهي الاشتراك، يجب التجديد.'
  }
];

const INITIAL_SERVICES = [
  { id: 'S-1', name: 'جلسة تقييم وعلاج طبيعي عام', category: 'بالغين', priceDaily: 150, price10Days: 1200, priceMonthly: 2500, description: 'فحص شامل وتحديد الخطة + أجهزة أساسية' },
  { id: 'S-2', name: 'تأهيل إصابات العظام والجراحات', category: 'تأهيل جراحي', priceDaily: 180, price10Days: 1500, priceMonthly: 3000, description: 'جلسة متقدمة تقوية وتحريك مفاصل مع أجهزة صدمية' },
  { id: 'S-3', name: 'جلسة تأهيل الأطفال (تأهيل حركي)', category: 'أطفال', priceDaily: 140, price10Days: 1100, priceMonthly: 2200, description: 'تمارين تحفيز النمو الحركي والتوازن للأطفال' },
  { id: 'S-4', name: 'الوخز بالإبر الجافة (Dry Needling)', category: 'علاجات متخصصة', priceDaily: 120, price10Days: 950, priceMonthly: 1800, description: 'فك العقد العضلية والتشنجات المزمنة' },
  { id: 'S-5', name: 'جلسة العلاج بالموجات الصدمية (Shockwave)', category: 'أجهزة متقدمة', priceDaily: 200, price10Days: 1600, priceMonthly: 3200, description: 'لعلاج شوكة العقب والتكلسات والتهاب الأوتار' },
  { id: 'S-6', name: 'الحجامة الطبية والتدليك العلاجي', category: 'علاجات مكملة', priceDaily: 130, price10Days: 1000, priceMonthly: 2000, description: 'تنشيط الدورة الدموية وتخفيف الآلام العضلية' },
];

const INITIAL_SCHEDULE = [
  { id: 'AP-1', patientName: 'أحمد محمود السقاف', time: '09:00 ص', service: 'جلسة شد الفقرات والتنبيه', therapist: 'د. خالد العمري', status: 'completed' },
  { id: 'AP-2', patientName: 'سارة عبد الله الحسين', time: '10:30 ص', service: 'تأهيل الرباط الصليبي', therapist: 'د. منيرة الفاضل', status: 'completed' },
  { id: 'AP-3', patientName: 'عمر فاروق التميمي', time: '11:45 ص', service: 'علاج خشونة الركبة', therapist: 'د. خالد العمري', status: 'in_progress' },
  { id: 'AP-4', patientName: 'فاطمة محمد الزهراني', time: '04:00 م', service: 'تأهيل أطفال وحركة', therapist: 'د. ريم السالم', status: 'waiting' },
  { id: 'AP-5', patientName: 'يوسف حسن عبد العزيز', time: '05:30 م', service: 'جلسة موجات صدمية', therapist: 'د. منيرة الفاضل', status: 'waiting' },
  { id: 'AP-6', patientName: 'خالد عبد الرحمن', time: '06:45 م', service: 'كشف جديد واستشارة', therapist: 'د. خالد العمري', status: 'cancelled' },
];

const INITIAL_EXPENSES = [
  { id: 'EX-101', title: 'صيانة جهاز الموجات الصدمية', category: 'صيانة ومعدات', amount: 450, date: '2026-10-28', paidBy: 'د. خالد', notes: 'تغيير قطع استهلاكية مقبض التردد' },
  { id: 'EX-102', title: 'مستلزمات طبية (جل سونار، أقطاب الكهرباء)', category: 'مستلزمات طبية', amount: 320, date: '2026-10-25', paidBy: 'الإدارة', notes: 'شراء كرتون جل 5 لتر + أقطاب TENS' },
  { id: 'EX-103', title: 'فاتورة الكهرباء والمولد', category: 'فواتير وتشغيل', amount: 1100, date: '2026-10-20', paidBy: 'المحاسب', notes: 'شهر أكتوبر' },
  { id: 'EX-104', title: 'إيجار المقر - الدفعة الشهرية', category: 'إيجارات', amount: 4000, date: '2026-10-01', paidBy: 'الإدارة', notes: 'إيجار المبنى الرئيسي' },
  { id: 'EX-105', title: 'ضيافة المراجعين ونثريات', category: 'نثريات', amount: 180, date: '2026-10-27', paidBy: 'الاستقبال', notes: 'قهوة وشاي ومستلزمات ضيافة' },
];

const INITIAL_STAFF = [
  { id: 'ST-1', name: 'د. خالد العمري', role: 'أخصائي أول علاج طبيعي', phone: '0501112233', salary: 7500, status: 'حاضر', attendanceDays: 24, deductions: 0, bonuses: 500 },
  { id: 'ST-2', name: 'د. منيرة الفاضل', role: 'أخصائية علاج طبيعي وتأهيل', phone: '0502223344', salary: 6800, status: 'حاضر', attendanceDays: 25, deductions: 0, bonuses: 300 },
  { id: 'ST-3', name: 'د. ريم السالم', role: 'أخصائية تأهيل أطفال', phone: '0503334455', salary: 6500, status: 'حاضر', attendanceDays: 23, deductions: 100, bonuses: 200 },
  { id: 'ST-4', name: 'أحمد الشمري', role: 'موظف استقبال ومواعيد', phone: '0504445566', salary: 4000, status: 'حاضر', attendanceDays: 25, deductions: 0, bonuses: 150 },
  { id: 'ST-5', name: 'سعيد الحربي', role: 'فني صيانة ومساعد', phone: '0505556677', salary: 3500, status: 'غائب', attendanceDays: 22, deductions: 150, bonuses: 0 },
];

export default function App() {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [patients, setPatients] = useState(INITIAL_PATIENTS);
  const [services, setServices] = useState(INITIAL_SERVICES);
  const [schedule, setSchedule] = useState(INITIAL_SCHEDULE);
  const [expenses, setExpenses] = useState(INITIAL_EXPENSES);
  const [staff, setStaff] = useState(INITIAL_STAFF);

  // حقول تصفية وبحث
  const [patientSearch, setPatientSearch] = useState('');
  const [selectedDiagnosisFilter, setSelectedDiagnosisFilter] = useState('all');

  // نماذج الإضافة (Modals)
  const [showAddPatientModal, setShowAddPatientModal] = useState(false);
  const [showAddExpenseModal, setShowAddExpenseModal] = useState(false);
  const [showAddAppointmentModal, setShowAddAppointmentModal] = useState(false);
  const [showApkModal, setShowApkModal] = useState(false);
  const [selectedPatientForView, setSelectedPatientForView] = useState(null);

  // حالة نموذج مريض جديد
  const [newPatient, setNewPatient] = useState({
    name: '', phone: '', age: '', gender: 'ذكر', diagnosis: '', planType: '10_days',
    planTitle: 'باقة 10 أيام', totalSessions: 10, paidAmount: '', therapist: 'د. خالد العمري',
    devicesUsed: []
  });

  // حالة نموذج مصروف جديد
  const [newExpense, setNewExpense] = useState({
    title: '', category: 'مستلزمات طبية', amount: '', paidBy: 'الإدارة', notes: ''
  });

  // حساب الحسابات المالية السريعة
  const totalRevenue = useMemo(() => {
    return patients.reduce((acc, p) => acc + (p.paidAmount || 0), 0);
  }, [patients]);

  const totalExpenseSum = useMemo(() => {
    return expenses.reduce((acc, e) => acc + (e.amount || 0), 0);
  }, [expenses]);

  const totalSalaries = useMemo(() => {
    return staff.reduce((acc, s) => acc + (s.salary + s.bonuses - s.deductions), 0);
  }, [staff]);

  const netProfit = totalRevenue - (totalExpenseSum + totalSalaries);

  const activePatientsCount = patients.filter(p => p.status === 'active' || p.status === 'expiring_soon').length;
  const expiringSoonCount = patients.filter(p => p.completedSessions >= p.totalSessions - 2).length;

  // إضافة مريض جديد
  const handleAddPatient = (e) => {
    e.preventDefault();
    if (!newPatient.name || !newPatient.phone) return;

    const added = {
      id: `P-${100 + patients.length + 1}`,
      name: newPatient.name,
      phone: newPatient.phone,
      age: Number(newPatient.age) || 30,
      gender: newPatient.gender,
      diagnosis: newPatient.diagnosis || 'تقييم كشف أول',
      planType: newPatient.planType,
      planTitle: newPatient.planType === 'daily' ? 'دفع يومي' : newPatient.planType === '10_days' ? 'اشتراك 10 أيام' : 'اشتراك شهري',
      totalSessions: Number(newPatient.totalSessions) || 10,
      completedSessions: 0,
      startDate: new Date().toISOString().split('T')[0],
      endDate: '2026-11-30',
      paidAmount: Number(newPatient.paidAmount) || 0,
      remainingAmount: 0,
      status: 'active',
      improvementRate: 10,
      devicesUsed: ['التنبيه الكهربائي TENS', 'الموجات الصوتية US'],
      therapist: newPatient.therapist,
      notes: 'تم افتتاح ملف مريض جديد وتسجيل الرسوم.'
    };

    setPatients([added, ...patients]);
    setShowAddPatientModal(false);
    setNewPatient({ name: '', phone: '', age: '', gender: 'ذكر', diagnosis: '', planType: '10_days', planTitle: '', totalSessions: 10, paidAmount: '', therapist: 'د. خالد العمري', devicesUsed: [] });
  };

  // إضافة مصروف جديد
  const handleAddExpense = (e) => {
    e.preventDefault();
    if (!newExpense.title || !newExpense.amount) return;

    const exp = {
      id: `EX-${100 + expenses.length + 1}`,
      title: newExpense.title,
      category: newExpense.category,
      amount: Number(newExpense.amount),
      date: new Date().toISOString().split('T')[0],
      paidBy: newExpense.paidBy,
      notes: newExpense.notes || '-'
    };

    setExpenses([exp, ...expenses]);
    setShowAddExpenseModal(false);
    setNewExpense({ title: '', category: 'مستلزمات طبية', amount: '', paidBy: 'الإدارة', notes: '' });
  };

  // تحديث جلسات المريض (إضافة جلسة مكتملة)
  const handleIncrementSession = (patientId) => {
    setPatients(patients.map(p => {
      if (p.id === patientId) {
        const nextCompleted = Math.min(p.completedSessions + 1, p.totalSessions);
        const nextImprovement = Math.min(p.improvementRate + 5, 100);
        return {
          ...p,
          completedSessions: nextCompleted,
          improvementRate: nextImprovement,
          status: nextCompleted >= p.totalSessions ? 'completed' : (nextCompleted >= p.totalSessions - 2 ? 'expiring_soon' : 'active')
        };
      }
      return p;
    }));
  };

  // تغيير حالة الموعد اليومي
  const handleAppointmentStatusChange = (id, newStatus) => {
    setSchedule(schedule.map(item => item.id === id ? { ...item, status: newStatus } : item));
  };

  // المراجعين المفلترين للبحث
  const filteredPatients = patients.filter(p => {
    const matchesSearch = p.name.includes(patientSearch) || p.phone.includes(patientSearch) || p.id.includes(patientSearch);
    return matchesSearch;
  });

  return (
    <div className="min-h-screen bg-slate-50 text-slate-800 dir-rtl font-sans pb-12" dir="rtl">
      
      {/* === الهيدر العلوي والشعار === */}
      <header className="bg-gradient-to-r from-teal-800 via-teal-700 to-cyan-800 text-white shadow-lg sticky top-0 z-40">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex items-center justify-between">
          <div className="flex items-center space-x-3 space-x-reverse">
            <div className="bg-white/10 p-2.5 rounded-xl backdrop-blur-md border border-white/20 flex items-center justify-center">
              <Activity className="w-8 h-8 text-teal-200 animate-pulse" />
            </div>
            <div>
              <div className="flex items-center space-x-2 space-x-reverse">
                <h1 className="text-2xl font-black tracking-wide text-white">مركز كيروان</h1>
                <span className="bg-cyan-400/30 text-cyan-100 text-xs px-2.5 py-0.5 rounded-full font-semibold border border-cyan-300/30">
                  للعلاج الطبيعي والتأهيل
                </span>
              </div>
              <p className="text-xs text-teal-100/80 font-medium mt-0.5">Kirwan Physical Therapy & Rehabilitation System</p>
            </div>
          </div>

          {/* معلومات سريعة في الهيدر وزر التحميل كـ APK */}
          <div className="flex items-center space-x-3 space-x-reverse text-sm">
            <button
              onClick={() => setShowApkModal(true)}
              className="bg-cyan-500 hover:bg-cyan-400 text-teal-950 font-black px-3.5 py-2 rounded-lg shadow-md border border-cyan-300/40 flex items-center gap-2 transition text-xs sm:text-sm animate-bounce"
            >
              <Smartphone className="w-4 h-4" />
              <span>تحميل تطبيق أندرويد (APK)</span>
            </button>

            <button 
              onClick={() => setShowAddPatientModal(true)}
              className="hidden md:flex bg-emerald-500 hover:bg-emerald-600 text-white font-bold px-4 py-2 rounded-lg shadow border border-emerald-400/40 items-center gap-2 transition duration-200 text-sm active:scale-95"
            >
              <UserPlus className="w-4 h-4" />
              <span>مريض جديد</span>
            </button>
          </div>
        </div>

        {/* ... existing navigation bar code ... */}
        <nav className="bg-teal-900/60 backdrop-blur-md border-t border-teal-600/30 px-4 sm:px-8">
          <div className="max-w-7xl mx-auto flex space-x-1 space-x-reverse overflow-x-auto no-scrollbar py-1">
            {[
              { id: 'dashboard', label: 'لوحة التحكم والإحصائيات', icon: Activity },
              { id: 'patients', label: 'المراجعين والاشتراكات', icon: Users },
              { id: 'schedule', label: 'المواعيد والانتظار اليومي', icon: Clock },
              { id: 'services', label: 'دليل الخدمات والأسعار', icon: Stethoscope },
              { id: 'financials', label: 'المصاريف والمالية', icon: DollarSign },
              { id: 'hr', label: 'الكادر الطبي والرواتب', icon: Briefcase },
            ].map((tab) => {
              const Icon = tab.icon;
              const isActive = activeTab === tab.id;
              return (
                <button
                  key={tab.id}
                  onClick={() => setActiveTab(tab.id)}
                  className={`flex items-center space-x-2 space-x-reverse px-4 py-2.5 rounded-lg text-xs md:text-sm font-bold whitespace-nowrap transition-all duration-200 ${
                    isActive 
                      ? 'bg-white text-teal-900 shadow-md transform -translate-y-0.5' 
                      : 'text-teal-100 hover:bg-white/10 hover:text-white'
                  }`}
                >
                  <Icon className={`w-4 h-4 ${isActive ? 'text-teal-700' : 'text-teal-300'}`} />
                  <span>{tab.label}</span>
                </button>
              );
            })}
          </div>
        </nav>
      </header>

      {/* ... existing main body code ... */}
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-6">
        {/* ... existing tabs content ... */}
        {activeTab === 'dashboard' && (
          <div className="space-y-6">
            {/* بطاقة تنبيه تثبيت التطبيق على أجهزة أندرويد */}
            <div className="bg-gradient-to-r from-cyan-900 to-teal-900 border border-cyan-500/30 p-4 rounded-2xl text-white flex flex-col sm:flex-row items-center justify-between gap-4 shadow-md">
              <div className="flex items-center gap-3">
                <div className="p-3 bg-cyan-500/20 rounded-xl border border-cyan-400/30">
                  <Smartphone className="w-7 h-7 text-cyan-300" />
                </div>
                <div>
                  <h3 className="font-bold text-base">تريد تحويل هذا النظام إلى تطبيق Android (APK) يعمل على الهاتف؟</h3>
                  <p className="text-xs text-slate-300 mt-0.5">يمكنك تثبيته كتطبيق مستقل على جوالات الأخصائيين والموظفين بالمركز.</p>
                </div>
              </div>
              <button
                onClick={() => setShowApkModal(true)}
                className="bg-cyan-400 hover:bg-cyan-300 text-slate-950 font-black px-4 py-2 rounded-xl text-xs whitespace-nowrap transition shadow"
              >
                خطوات تحويل التطبيق لـ APK
              </button>
            </div>

            {/* ... rest of dashboard content ... */}
            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
              <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200/80 hover:shadow-md transition">
                <div className="flex items-center justify-between">
                  <div>
                    <p className="text-xs font-bold text-slate-500 uppercase tracking-wider">إجمالي الإيرادات</p>
                    <h3 className="text-2xl font-black text-slate-800 mt-1">{totalRevenue.toLocaleString()} <span className="text-xs font-semibold text-slate-500">ر.س</span></h3>
                  </div>
                  <div className="w-12 h-12 bg-emerald-100 text-emerald-600 rounded-xl flex items-center justify-center">
                    <TrendingUp className="w-6 h-6" />
                  </div>
                </div>
                <div className="mt-3 flex items-center text-xs text-emerald-600 font-semibold gap-1">
                  <ArrowUpRight className="w-4 h-4" />
                  <span>+14% مقارنة بالشهر الماضي</span>
                </div>
              </div>

              <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200/80 hover:shadow-md transition">
                <div className="flex items-center justify-between">
                  <div>
                    <p className="text-xs font-bold text-slate-500 uppercase tracking-wider">إجمالي المصاريف</p>
                    <h3 className="text-2xl font-black text-rose-600 mt-1">{(totalExpenseSum + totalSalaries).toLocaleString()} <span className="text-xs font-semibold text-slate-500">ر.س</span></h3>
                  </div>
                  <div className="w-12 h-12 bg-rose-100 text-rose-600 rounded-xl flex items-center justify-center">
                    <TrendingDown className="w-6 h-6" />
                  </div>
                </div>
                <div className="mt-3 flex items-center text-xs text-slate-500 font-medium">
                  <span>تشمل التشغيل والرواتب الشهرية</span>
                </div>
              </div>

              <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200/80 hover:shadow-md transition">
                <div className="flex items-center justify-between">
                  <div>
                    <p className="text-xs font-bold text-slate-500 uppercase tracking-wider">المراجعين النشطين</p>
                    <h3 className="text-2xl font-black text-teal-700 mt-1">{activePatients
