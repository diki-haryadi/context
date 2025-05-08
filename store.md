/== affiliateStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

// Types
export type AffiliateStatus = 'active' | 'pending' | 'inactive' | 'rejected';
export type AffiliatorLevel = 'bronze' | 'silver' | 'gold' | 'platinum';
export type PaymentMethod = 'bank_transfer' | 'e_wallet' | 'paypal';
export type PaymentStatus = 'pending' | 'processing' | 'completed' | 'failed';
export type MaterialType = 'banner' | 'email' | 'text' | 'social';

export interface AffiliateProgram {
  id: string;
  name: string;
  referralCode: string;
  commission: string;
  cookieDuration: number;
  description: string;
  terms: string;
  status: AffiliateStatus;
  paymentSchedule: 'weekly' | 'monthly' | 'quarterly';
  multilevel: boolean;
  level2Commission: string;
  minPayoutAmount: number;
  createdAt: string;
  updatedAt: string;
  registrations: number;
  conversions: number;
  earnings: number;
}

export interface Affiliator {
  id: string;
  userId: string;
  name: string;
  email: string;
  phone: string;
  website: string;
  socialMedia: {
    instagram: string;
    facebook: string;
    twitter: string;
    linkedin: string;
    youtube: string;
  };
  profilePicture: string;
  balance: number;
  availableBalance: number;
  pendingBalance: number;
  totalEarnings: number;
  referralCode: string;
  referralLink: string;
  totalReferrals: number;
  totalConversions: number;
  conversionRate: number;
  status: AffiliateStatus;
  level: AffiliatorLevel;
  progress: number;
  nextLevelThreshold: number;
  paymentMethod: PaymentMethod;
  paymentDetails: {
    bankName?: string;
    accountNumber?: string;
    accountHolderName?: string;
    eWalletProvider?: string;
    eWalletNumber?: string;
    paypalEmail?: string;
  };
  registrationDate: string;
  approvalDate: string | null;
  notes: string;
}

export interface PromotionalMaterial {
  id: string;
  programId: string;
  name: string;
  type: MaterialType;
  preview: string;
  description: string;
  createdAt: string;
  updatedAt: string;
}

export interface Referral {
  id: string;
  affiliatorId: string;
  affiliatorName: string;
  referredUserId: string;
  referredUserName: string;
  referredUserEmail: string;
  date: string;
  status: 'pending' | 'converted' | 'expired';
  commission: number;
  programId: string;
  programName: string;
  conversionDate: string | null;
}

export interface Payout {
  id: string;
  affiliatorId: string;
  affiliatorName: string;
  amount: number;
  status: PaymentStatus;
  date: string;
  processedDate: string | null;
  method: PaymentMethod;
  reference: string;
  notes: string;
}

export interface AffiliateSettings {
  globalSettings: {
    enabled: boolean;
    defaultCommission: number;
    defaultCookieDuration: number;
    minPayoutAmount: number;
    multilevelEnabled: boolean;
    applicationRequiresApproval: boolean;
    defaultPaymentSchedule: 'weekly' | 'monthly' | 'quarterly';
  };
  paymentMethods: {
    bankTransfer: boolean;
    eWallet: boolean;
    paypal: boolean;
  };
  levelSettings: {
    enabled: boolean;
    levels: {
      bronze: {
        minReferrals: number;
        bonusCommission: number;
      };
      silver: {
        minReferrals: number;
        bonusCommission: number;
      };
      gold: {
        minReferrals: number;
        bonusCommission: number;
      };
      platinum: {
        minReferrals: number;
        bonusCommission: number;
      };
    };
  };
  termsAndConditions: string;
  applicationFormFields: {
    website: boolean;
    phone: boolean;
    socialMedia: boolean;
    marketingPlan: boolean;
  };
}

export interface AffiliateAnalytics {
  totalEarnings: number;
  totalRegistrations: number;
  totalConversions: number;
  conversionRate: number;
  monthlyStats: {
    name: string;
    registrations: number;
    conversions: number;
    earnings: number;
  }[];
  topAffiliates: {
    id: string;
    name: string;
    earnings: number;
    conversions: number;
    conversionRate: number;
  }[];
}

interface AffiliateState {
  // Data
  settings: AffiliateSettings;
  programs: AffiliateProgram[];
  affiliators: Affiliator[];
  promotionalMaterials: PromotionalMaterial[];
  referrals: Referral[];
  payouts: Payout[];
  analytics: AffiliateAnalytics;
  
  // Current selections
  currentProgramId: string | null;
  currentAffiliatorId: string | null;
  
  // UI states
  isLoading: boolean;
  error: string | null;

  // Program actions
  addProgram: (program: Omit<AffiliateProgram, 'id' | 'createdAt' | 'updatedAt' | 'registrations' | 'conversions' | 'earnings'>) => void;
  updateProgram: (id: string, programData: Partial<AffiliateProgram>) => void;
  deleteProgram: (id: string) => void;
  
  // Affiliator actions
  addAffiliator: (affiliator: Omit<Affiliator, 'id' | 'registrationDate' | 'approvalDate' | 'totalEarnings' | 'totalConversions' | 'totalReferrals' | 'conversionRate'>) => void;
  updateAffiliator: (id: string, affiliatorData: Partial<Affiliator>) => void;
  deleteAffiliator: (id: string) => void;
  approveAffiliator: (id: string) => void;
  rejectAffiliator: (id: string) => void;
  
  // Promotional materials actions
  addPromotionalMaterial: (material: Omit<PromotionalMaterial, 'id' | 'createdAt' | 'updatedAt'>) => void;
  updatePromotionalMaterial: (id: string, materialData: Partial<PromotionalMaterial>) => void;
  deletePromotionalMaterial: (id: string) => void;
  
  // Referral actions
  addReferral: (referral: Omit<Referral, 'id' | 'conversionDate'>) => void;
  updateReferral: (id: string, referralData: Partial<Referral>) => void;
  convertReferral: (id: string) => void;
  
  // Payout actions
  createPayout: (payout: Omit<Payout, 'id' | 'processedDate'>) => void;
  updatePayoutStatus: (id: string, status: PaymentStatus) => void;
  
  // Settings actions
  updateSettings: (newSettings: Partial<AffiliateSettings>) => void;
  
  // Analytics actions
  refreshAnalytics: () => void;
}

// Sample data - Affiliate Programs
const samplePrograms: AffiliateProgram[] = [
  {
    id: "1",
    name: "Program Influencer",
    referralCode: "INF2024",
    commission: "15%",
    cookieDuration: 30,
    description: "Program afiliasi khusus untuk influencer dengan jangkauan minimal 10.000 followers",
    terms: "Influencer harus mempromosikan kursus minimal 2 kali dalam sebulan",
    status: "active",
    paymentSchedule: "monthly",
    multilevel: false,
    level2Commission: "",
    minPayoutAmount: 300000,
    createdAt: "2025-01-15T08:00:00Z",
    updatedAt: "2025-04-01T10:30:00Z",
    registrations: 25,
    conversions: 18,
    earnings: 3750000,
  },
  {
    id: "2",
    name: "Program Kampus",
    referralCode: "CAMPUS2024",
    commission: "20%",
    cookieDuration: 45,
    description: "Program afiliasi untuk mahasiswa dan institusi pendidikan",
    terms: "Terbuka untuk mahasiswa, dosen, dan staf kampus",
    status: "active",
    paymentSchedule: "monthly",
    multilevel: true,
    level2Commission: "5%",
    minPayoutAmount: 200000,
    createdAt: "2025-01-20T09:00:00Z",
    updatedAt: "2025-04-05T11:15:00Z",
    registrations: 42,
    conversions: 35,
    earnings: 5250000,
  },
  {
    id: "3",
    name: "Program Komunitas",
    referralCode: "COMM2024",
    commission: "12%",
    cookieDuration: 30,
    description: "Program afiliasi untuk komunitas IT dan programming",
    terms: "Terbuka untuk grup atau komunitas dengan minimal 50 anggota",
    status: "pending",
    paymentSchedule: "monthly",
    multilevel: false,
    level2Commission: "",
    minPayoutAmount: 250000,
    createdAt: "2025-02-10T14:00:00Z",
    updatedAt: "2025-02-10T14:00:00Z",
    registrations: 15,
    conversions: 8,
    earnings: 1200000,
  },
];

// Sample data - Affiliators
const sampleAffiliators: Affiliator[] = [
  {
    id: "1",
    userId: "user123",
    name: "John Doe",
    email: "john.doe@example.com",
    phone: "+6281234567890",
    website: "https://johndoeblog.com",
    socialMedia: {
      instagram: "@johndoe_tech",
      facebook: "johndoe.tech",
      twitter: "@johndoe_tech",
      linkedin: "johndoe-tech",
      youtube: "JohnDoeTech",
    },
    profilePicture: "https://i.pravatar.cc/300?img=1",
    balance: 1500000,
    availableBalance: 1000000,
    pendingBalance: 500000,
    totalEarnings: 2500000,
    referralCode: "JOHNDOE2024",
    referralLink: "https://example.com/ref/JOHNDOE2024",
    totalReferrals: 18,
    totalConversions: 12,
    conversionRate: 66.7,
    status: "active",
    level: "silver",
    progress: 12,
    nextLevelThreshold: 20,
    paymentMethod: "bank_transfer",
    paymentDetails: {
      bankName: "BCA",
      accountNumber: "1234567890",
      accountHolderName: "John Doe",
    },
    registrationDate: "2025-01-18T08:30:00Z",
    approvalDate: "2025-01-19T10:15:00Z",
    notes: "Tech blogger dengan audience fokus di web development",
  },
  {
    id: "2",
    userId: "user456",
    name: "Jane Smith",
    email: "jane.smith@example.com",
    phone: "+6289876543210",
    website: "https://janesmith.dev",
    socialMedia: {
      instagram: "@janesmith_dev",
      facebook: "janesmith.dev",
      twitter: "@janesmith_dev",
      linkedin: "janesmith-dev",
      youtube: "JaneSmithDev",
    },
    profilePicture: "https://i.pravatar.cc/300?img=5",
    balance: 2000000,
    availableBalance: 1500000,
    pendingBalance: 500000,
    totalEarnings: 3500000,
    referralCode: "JANESMITH2024",
    referralLink: "https://example.com/ref/JANESMITH2024",
    totalReferrals: 25,
    totalConversions: 20,
    conversionRate: 80.0,
    status: "active",
    level: "gold",
    progress: 20,
    nextLevelThreshold: 30,
    paymentMethod: "e_wallet",
    paymentDetails: {
      eWalletProvider: "GoPay",
      eWalletNumber: "089876543210",
    },
    registrationDate: "2025-01-10T15:45:00Z",
    approvalDate: "2025-01-11T09:30:00Z",
    notes: "YouTuber dengan fokus tutorial programming",
  },
  {
    id: "3",
    userId: "user789",
    name: "Robert Garcia",
    email: "robert.garcia@example.com",
    phone: "+6282345678901",
    website: "https://roberttech.com",
    socialMedia: {
      instagram: "@robert_tech",
      facebook: "robert.tech",
      twitter: "@robert_tech",
      linkedin: "robert-garcia-tech",
      youtube: "",
    },
    profilePicture: "https://i.pravatar.cc/300?img=8",
    balance: 800000,
    availableBalance: 500000,
    pendingBalance: 300000,
    totalEarnings: 1200000,
    referralCode: "ROBERTG2024",
    referralLink: "https://example.com/ref/ROBERTG2024",
    totalReferrals: 10,
    totalConversions: 7,
    conversionRate: 70.0,
    status: "active",
    level: "bronze",
    progress: 7,
    nextLevelThreshold: 10,
    paymentMethod: "bank_transfer",
    paymentDetails: {
      bankName: "Mandiri",
      accountNumber: "9876543210",
      accountHolderName: "Robert Garcia",
    },
    registrationDate: "2025-02-05T11:20:00Z",
    approvalDate: "2025-02-06T14:10:00Z",
    notes: "IT Consultant dengan network di perusahaan teknologi",
  },
  {
    id: "4",
    userId: "user101",
    name: "Sarah Johnson",
    email: "sarah.johnson@example.com",
    phone: "+6283456789012",
    website: "",
    socialMedia: {
      instagram: "@sarah_codes",
      facebook: "",
      twitter: "@sarah_codes",
      linkedin: "sarah-johnson-dev",
      youtube: "",
    },
    profilePicture: "https://i.pravatar.cc/300?img=9",
    balance: 0,
    availableBalance: 0,
    pendingBalance: 0,
    totalEarnings: 0,
    referralCode: "SARAHJ2024",
    referralLink: "https://example.com/ref/SARAHJ2024",
    totalReferrals: 0,
    totalConversions: 0,
    conversionRate: 0,
    status: "pending",
    level: "bronze",
    progress: 0,
    nextLevelThreshold: 10,
    paymentMethod: "paypal",
    paymentDetails: {
      paypalEmail: "sarah.johnson@example.com",
    },
    registrationDate: "2025-04-02T09:15:00Z",
    approvalDate: null,
    notes: "Menunggu verifikasi dokumen",
  },
  {
    id: "5",
    userId: "user202",
    name: "David Lee",
    email: "david.lee@example.com",
    phone: "+6285678901234",
    website: "https://davidcodes.io",
    socialMedia: {
      instagram: "@david_codes",
      facebook: "david.codes",
      twitter: "@david_codes",
      linkedin: "david-lee-dev",
      youtube: "DavidCodesTutorials",
    },
    profilePicture: "https://i.pravatar.cc/300?img=3",
    balance: 0,
    availableBalance: 0,
    pendingBalance: 0,
    totalEarnings: 0,
    referralCode: "DAVIDL2024",
    referralLink: "https://example.com/ref/DAVIDL2024",
    totalReferrals: 0,
    totalConversions: 0,
    conversionRate: 0,
    status: "inactive",
    level: "bronze",
    progress: 0,
    nextLevelThreshold: 10,
    paymentMethod: "bank_transfer",
    paymentDetails: {
      bankName: "BNI",
      accountNumber: "5678901234",
      accountHolderName: "David Lee",
    },
    registrationDate: "2025-02-10T16:40:00Z",
    approvalDate: "2025-02-12T08:25:00Z",
    notes: "Akun dinonaktifkan karena tidak aktif selama 60 hari",
  }
];

// Sample referrals
const sampleReferrals: Referral[] = [
  {
    id: "1",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    referredUserId: "user001",
    referredUserName: "Sarah Johnson",
    referredUserEmail: "sarah@example.com",
    date: "2025-04-08T10:30:00Z",
    status: "converted",
    commission: 250000,
    programId: "1",
    programName: "Program Influencer",
    conversionDate: "2025-04-10T14:15:00Z",
  },
  {
    id: "2",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    referredUserId: "user002",
    referredUserName: "David Williams",
    referredUserEmail: "david@example.com",
    date: "2025-04-05T12:45:00Z",
    status: "converted",
    commission: 250000,
    programId: "1",
    programName: "Program Influencer",
    conversionDate: "2025-04-06T09:20:00Z",
  },
  {
    id: "3",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    referredUserId: "user003",
    referredUserName: "Michael Brown",
    referredUserEmail: "michael@example.com",
    date: "2025-04-02T15:10:00Z",
    status: "converted",
    commission: 250000,
    programId: "1",
    programName: "Program Influencer",
    conversionDate: "2025-04-03T18:30:00Z",
  },
  {
    id: "4",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    referredUserId: "user004",
    referredUserName: "Emily Davis",
    referredUserEmail: "emily@example.com",
    date: "2025-04-30T11:25:00Z",
    status: "pending",
    commission: 250000,
    programId: "1",
    programName: "Program Influencer",
    conversionDate: null,
  },
  {
    id: "5",
    affiliatorId: "2",
    affiliatorName: "Jane Smith",
    referredUserId: "user005",
    referredUserName: "James Wilson",
    referredUserEmail: "james@example.com",
    date: "2025-04-28T14:50:00Z",
    status: "converted",
    commission: 300000,
    programId: "2",
    programName: "Program Kampus",
    conversionDate: "2025-04-29T16:40:00Z",
  },
  {
    id: "6",
    affiliatorId: "2",
    affiliatorName: "Jane Smith",
    referredUserId: "user006",
    referredUserName: "Jennifer Lee",
    referredUserEmail: "jennifer@example.com",
    date: "2025-04-25T09:15:00Z",
    status: "expired",
    commission: 0,
    programId: "2",
    programName: "Program Kampus",
    conversionDate: null,
  },
  {
    id: "7",
    affiliatorId: "3",
    affiliatorName: "Robert Garcia",
    referredUserId: "user007",
    referredUserName: "Robert Johnson",
    referredUserEmail: "robert@example.com",
    date: "2025-04-20T13:40:00Z",
    status: "converted",
    commission: 180000,
    programId: "3",
    programName: "Program Komunitas",
    conversionDate: "2025-04-22T10:30:00Z",
  }
];

// Sample payouts
const samplePayouts: Payout[] = [
  {
    id: "PAY-123456",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    amount: 750000,
    status: "completed",
    date: "2025-05-01T09:00:00Z",
    processedDate: "2025-05-03T14:30:00Z",
    method: "bank_transfer",
    reference: "TRF25050301",
    notes: "Pembayaran komisi bulan April 2025",
  },
  {
    id: "PAY-123457",
    affiliatorId: "1",
    affiliatorName: "John Doe",
    amount: 500000,
    status: "processing",
    date: "2025-04-01T10:15:00Z",
    processedDate: null,
    method: "bank_transfer",
    reference: "TRF25040105",
    notes: "Pembayaran komisi bulan Maret 2025",
  },
  {
    id: "PAY-123458",
    affiliatorId: "2",
    affiliatorName: "Jane Smith",
    amount: 1200000,
    status: "completed",
    date: "2025-05-01T09:30:00Z",
    processedDate: "2025-05-02T15:45:00Z",
    method: "e_wallet",
    reference: "GoPay25050201",
    notes: "Pembayaran komisi bulan April 2025",
  },
  {
    id: "PAY-123459",
    affiliatorId: "3",
    affiliatorName: "Robert Garcia",
    amount: 180000,
    status: "pending",
    date: "2025-05-01T11:20:00Z",
    processedDate: null,
    method: "bank_transfer",
    reference: "TRF25050106",
    notes: "Menunggu verifikasi informasi bank",
  }
];

// Sample promotional materials
const samplePromotionalMaterials: PromotionalMaterial[] = [
  {
    id: "1",
    programId: "1",
    name: "Banner Utama",
    type: "banner",
    preview: "https://via.placeholder.com/800x200?text=Main+Banner",
    description: "Banner berukuran 800x200px untuk digunakan di website",
    createdAt: "2025-02-15T09:00:00Z",
    updatedAt: "2025-02-15T09:00:00Z",
  },
  {
    id: "2",
    programId: "1",
    name: "Template Email",
    type: "email",
    preview: "Halo [Nama], Saya ingin merekomendasikan kursus coding yang sangat bagus di CodeCampus. Gunakan kode [REFERRAL_CODE] untuk mendapatkan diskon 10%!",
    description: "Template email untuk dikirim ke teman dan keluarga",
    createdAt: "2025-02-15T09:30:00Z",
    updatedAt: "2025-03-01T14:15:00Z",
  },
  {
    id: "3",
    programId: "1",
    name: "Post Instagram",
    type: "social",
    preview: "Belajar coding jadi lebih mudah dengan CodeCampus! 🚀 Daftar sekarang dengan kode referral [REFERRAL_CODE] dan dapatkan diskon 10%! #BelajarCoding #CodeCampus",
    description: "Konten untuk posting di Instagram dan platform sosial media lainnya",
    createdAt: "2025-02-15T10:00:00Z",
    updatedAt: "2025-02-15T10:00:00Z",
  },
  {
    id: "4",
    programId: "2",
    name: "Banner Kampus",
    type: "banner",
    preview: "https://via.placeholder.com/800x200?text=Campus+Banner",
    description: "Banner khusus untuk program kampus",
    createdAt: "2025-02-20T11:30:00Z",
    updatedAt: "2025-02-20T11:30:00Z",
  },
  {
    id: "5",
    programId: "2",
    name: "Flyer Digital",
    type: "banner",
    preview: "https://via.placeholder.com/400x600?text=Digital+Flyer",
    description: "Flyer digital untuk disebarkan via sosial media atau email",
    createdAt: "2025-02-20T12:00:00Z",
    updatedAt: "2025-03-15T09:45:00Z",
  }
];

// Sample settings
const sampleSettings: AffiliateSettings = {
  globalSettings: {
    enabled: true,
    defaultCommission: 15,
    defaultCookieDuration: 30,
    minPayoutAmount: 300000,
    multilevelEnabled: true,
    applicationRequiresApproval: true,
    defaultPaymentSchedule: "monthly",
  },
  paymentMethods: {
    bankTransfer: true,
    eWallet: true,
    paypal: true,
  },
  levelSettings: {
    enabled: true,
    levels: {
      bronze: {
        minReferrals: 0,
        bonusCommission: 0,
      },
      silver: {
        minReferrals: 10,
        bonusCommission: 2,
      },
      gold: {
        minReferrals: 20,
        bonusCommission: 5,
      },
      platinum: {
        minReferrals: 50,
        bonusCommission: 10,
      },
    },
  },
  termsAndConditions: "1. Afiliator harus mematuhi semua ketentuan dan kebijakan program afiliasi.\n2. Afiliator tidak diperkenankan untuk melakukan spam atau praktek pemasaran yang tidak etis.\n3. Afiliator tidak diperbolehkan menggunakan kata kunci merek atau variasinya dalam kampanye iklan PPC.\n4. Komisi hanya akan dibayarkan setelah mencapai batas minimum dan sesuai jadwal pembayaran yang ditetapkan.\n5. Perusahaan berhak untuk mengubah syarat dan ketentuan program afiliasi kapan saja.\n6. Perusahaan berhak untuk menolak atau memutus hubungan dengan afiliator yang melanggar ketentuan ini.",
  applicationFormFields: {
    website: true,
    phone: true,
    socialMedia: true,
    marketingPlan: true,
  },
};

// Sample analytics
const sampleAnalytics: AffiliateAnalytics = {
  totalEarnings: 10200000,
  totalRegistrations: 82,
  totalConversions: 61,
  conversionRate: 74.4,
  monthlyStats: [
    { name: "Jan", registrations: 12, conversions: 8, earnings: 1200000 },
    { name: "Feb", registrations: 15, conversions: 12, earnings: 1800000 },
    { name: "Mar", registrations: 21, conversions: 15, earnings: 2250000 },
    { name: "Apr", registrations: 34, conversions: 26, earnings: 4950000 },
  ],
  topAffiliates: [
    { id: "2", name: "Jane Smith", earnings: 3500000, conversions: 20, conversionRate: 80.0 },
    { id: "1", name: "John Doe", earnings: 2500000, conversions: 12, conversionRate: 66.7 },
    { id: "3", name: "Robert Garcia", earnings: 1200000, conversions: 7, conversionRate: 70.0 },
    { id: "5", name: "David Lee", earnings: 0, conversions: 0, conversionRate: 0 },
    { id: "4", name: "Sarah Johnson", earnings: 0, conversions: 0, conversionRate: 0 },
  ],
};

// Create the store
export const useAffiliateStore = create<AffiliateState>()(
  persist(
    (set, get) => ({
      // Initial state
      settings: sampleSettings,
      programs: samplePrograms,
      affiliators: sampleAffiliators,
      promotionalMaterials: samplePromotionalMaterials,
      referrals: sampleReferrals,
      payouts: samplePayouts,
      analytics: sampleAnalytics,
      currentProgramId: null,
      currentAffiliatorId: null,
      isLoading: false,
      error: null,

      // Program actions
      addProgram: (program) => {
        try {
          const now = new Date().toISOString();
          
          // Ensure all required fields are present and in the correct format
          const newProgram: AffiliateProgram = {
            ...program,
            id: Date.now().toString(),
            createdAt: now,
            updatedAt: now,
            registrations: 0,
            conversions: 0,
            earnings: 0,
            // Ensure numeric fields are numbers
            cookieDuration: typeof program.cookieDuration === 'string' 
              ? parseInt(program.cookieDuration, 10) 
              : program.cookieDuration,
            // Ensure commission has % symbol
            commission: program.commission.includes('%') 
              ? program.commission 
              : `${program.commission}%`,
            // Ensure level2Commission has % symbol if present
            level2Commission: program.level2Commission 
              ? (program.level2Commission.includes('%') 
                  ? program.level2Commission 
                  : `${program.level2Commission}%`) 
              : '',
            // Ensure minPayoutAmount is a number
            minPayoutAmount: typeof program.minPayoutAmount === 'string' 
              ? parseInt(program.minPayoutAmount, 10) 
              : program.minPayoutAmount,
          };

          console.log("Adding new program to store:", newProgram);

          set((state) => ({
            programs: [...state.programs, newProgram],
          }));
          
          return newProgram.id; // Return the new program ID for reference
        } catch (error) {
          console.error("Error in addProgram:", error);
          throw error; // Re-throw to allow handling in the UI
        }
      },

      updateProgram: (id, programData) => {
        set((state) => ({
          programs: state.programs.map((program) =>
            program.id === id
              ? { ...program, ...programData, updatedAt: new Date().toISOString() }
              : program
          ),
        }));
      },

      deleteProgram: (id) => {
        set((state) => ({
          programs: state.programs.filter((program) => program.id !== id),
        }));
      },

      // Affiliator actions
      addAffiliator: (affiliator) => {
        const now = new Date().toISOString();
        const newAffiliator: Affiliator = {
          ...affiliator,
          id: Date.now().toString(),
          registrationDate: now,
          approvalDate: null,
          totalEarnings: 0,
          totalConversions: 0,
          totalReferrals: 0,
          conversionRate: 0,
        };

        set((state) => ({
          affiliators: [...state.affiliators, newAffiliator],
        }));
      },

      updateAffiliator: (id, affiliatorData) => {
        set((state) => ({
          affiliators: state.affiliators.map((affiliator) =>
            affiliator.id === id ? { ...affiliator, ...affiliatorData } : affiliator
          ),
        }));
      },

      deleteAffiliator: (id) => {
        set((state) => ({
          affiliators: state.affiliators.filter((affiliator) => affiliator.id !== id),
        }));
      },

      approveAffiliator: (id) => {
        set((state) => ({
          affiliators: state.affiliators.map((affiliator) =>
            affiliator.id === id
              ? {
                  ...affiliator,
                  status: "active",
                  approvalDate: new Date().toISOString(),
                }
              : affiliator
          ),
        }));
      },

      rejectAffiliator: (id) => {
        set((state) => ({
          affiliators: state.affiliators.map((affiliator) =>
            affiliator.id === id
              ? {
                  ...affiliator,
                  status: "rejected",
                }
              : affiliator
          ),
        }));
      },

      // Promotional materials actions
      addPromotionalMaterial: (material) => {
        const now = new Date().toISOString();
        const newMaterial: PromotionalMaterial = {
          ...material,
          id: Date.now().toString(),
          createdAt: now,
          updatedAt: now,
        };

        set((state) => ({
          promotionalMaterials: [...state.promotionalMaterials, newMaterial],
        }));
      },

      updatePromotionalMaterial: (id, materialData) => {
        set((state) => ({
          promotionalMaterials: state.promotionalMaterials.map((material) =>
            material.id === id
              ? { ...material, ...materialData, updatedAt: new Date().toISOString() }
              : material
          ),
        }));
      },

      deletePromotionalMaterial: (id) => {
        set((state) => ({
          promotionalMaterials: state.promotionalMaterials.filter(
            (material) => material.id !== id
          ),
        }));
      },

      // Referral actions
      addReferral: (referral) => {
        const newReferral: Referral = {
          ...referral,
          id: Date.now().toString(),
          conversionDate: null,
        };

        set((state) => ({
          referrals: [...state.referrals, newReferral],
        }));
      },

      updateReferral: (id, referralData) => {
        set((state) => ({
          referrals: state.referrals.map((referral) =>
            referral.id === id ? { ...referral, ...referralData } : referral
          ),
        }));
      },

      convertReferral: (id) => {
        set((state) => {
          const referral = state.referrals.find((r) => r.id === id);
          if (!referral) return state;

          // Update referral
          const updatedReferrals = state.referrals.map((r) =>
            r.id === id
              ? {
                  ...r,
                  status: "converted" as const,
                  conversionDate: new Date().toISOString(),
                }
              : r
          );

          // Update affiliator stats
          const updatedAffiliators = state.affiliators.map((a) =>
            a.id === referral.affiliatorId
              ? {
                  ...a,
                  totalConversions: a.totalConversions + 1,
                  totalEarnings: a.totalEarnings + referral.commission,
                  pendingBalance: a.pendingBalance + referral.commission,
                  conversionRate: ((a.totalConversions + 1) / a.totalReferrals) * 100,
                }
              : a
          );

          return {
            referrals: updatedReferrals,
            affiliators: updatedAffiliators,
          };
        });
      },

      // Payout actions
      createPayout: (payout) => {
        const newPayout: Payout = {
          ...payout,
          id: `PAY-${Date.now().toString().substring(7)}`,
          processedDate: null,
        };

        set((state) => {
          // Update payout list
          const updatedPayouts = [...state.payouts, newPayout];

          // If status is completed, update affiliator balance
          let updatedAffiliators = [...state.affiliators];
          if (payout.status === "completed") {
            updatedAffiliators = state.affiliators.map((a) =>
              a.id === payout.affiliatorId
                ? {
                    ...a,
                    availableBalance: a.availableBalance - payout.amount,
                    balance: a.balance - payout.amount,
                  }
                : a
            );
          }

          return {
            payouts: updatedPayouts,
            affiliators: updatedAffiliators,
          };
        });
      },

      updatePayoutStatus: (id, status) => {
        set((state) => {
          const payout = state.payouts.find((p) => p.id === id);
          if (!payout) return state;

          // Update payout
          const updatedPayouts = state.payouts.map((p) =>
            p.id === id
              ? {
                  ...p,
                  status,
                  processedDate:
                    status === "completed" ? new Date().toISOString() : p.processedDate,
                }
              : p
          );

          // If status changed to completed, update affiliator balance
          let updatedAffiliators = [...state.affiliators];
          if (status === "completed" && payout.status !== "completed") {
            updatedAffiliators = state.affiliators.map((a) =>
              a.id === payout.affiliatorId
                ? {
                    ...a,
                    availableBalance: a.availableBalance - payout.amount,
                    balance: a.balance - payout.amount,
                  }
                : a
            );
          }
          // If status changed from completed, restore affiliator balance
          else if (status !== "completed" && payout.status === "completed") {
            updatedAffiliators = state.affiliators.map((a) =>
              a.id === payout.affiliatorId
                ? {
                    ...a,
                    availableBalance: a.availableBalance + payout.amount,
                    balance: a.balance + payout.amount,
                  }
                : a
            );
          }

          return {
            payouts: updatedPayouts,
            affiliators: updatedAffiliators,
          };
        });
      },

      // Settings actions
      updateSettings: (newSettings) => {
        set((state) => ({
          settings: {
            ...state.settings,
            ...newSettings,
          },
        }));
      },

      // Analytics actions
      refreshAnalytics: () => {
        // In a real app, this would make an API call to get analytics data
        set((state) => {
          // Calculate analytics from state data
          const totalEarnings = state.referrals
            .filter((r) => r.status === "converted")
            .reduce((sum, r) => sum + r.commission, 0);

          const totalRegistrations = state.referrals.length;
          
          const totalConversions = state.referrals.filter(
            (r) => r.status === "converted"
          ).length;
          
          const conversionRate = totalRegistrations > 0
            ? (totalConversions / totalRegistrations) * 100
            : 0;

          // Create monthly stats (simplified for this example)
          const currentYear = new Date().getFullYear();
          const monthlyStats = [
            { name: "Jan", registrations: 12, conversions: 8, earnings: 1200000 },
            { name: "Feb", registrations: 15, conversions: 12, earnings: 1800000 },
            { name: "Mar", registrations: 21, conversions: 15, earnings: 2250000 },
            { name: "Apr", registrations: 34, conversions: 26, earnings: 4950000 },
          ];

          // Calculate top affiliates
          const topAffiliates = state.affiliators
            .map((a) => ({
              id: a.id,
              name: a.name,
              earnings: a.totalEarnings,
              conversions: a.totalConversions,
              conversionRate: a.conversionRate,
            }))
            .sort((a, b) => b.earnings - a.earnings)
            .slice(0, 5);

          return {
            analytics: {
              totalEarnings,
              totalRegistrations,
              totalConversions,
              conversionRate,
              monthlyStats,
              topAffiliates,
            },
          };
        });
      },
    }),
    {
      name: 'affiliate-storage',
      partialize: (state) => ({
        settings: state.settings,
        programs: state.programs,
        affiliators: state.affiliators,
        promotionalMaterials: state.promotionalMaterials,
        referrals: state.referrals,
        payouts: state.payouts,
      }),
    }
  )
);

/== authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: string;
  name: string;
  email: string;
  profilePicture?: string;
  hasSeenTour?: boolean;
  role: 'user' | 'admin';
}

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
  mockUsers: User[];
  login: (email: string, password: string) => Promise<void>;
  register: (name: string, email: string, password: string) => Promise<void>;
  logout: () => void;
  clearError: () => void;
  completeTour: () => void;
}

// Mock user data for demonstration
const mockUsers = [
  {
    id: '1',
    name: 'Unpam',
    email: 'unpam@example.com',
    password: 'password123',
    profilePicture: 'https://ui-avatars.com/api/?name=U&background=0D8ABC&color=fff',
    hasSeenTour: false,
    role: 'user' as const
  },
  {
    id: '2',
    name: 'Admin',
    email: 'admin@example.com',
    password: 'admin123',
    profilePicture: 'https://ui-avatars.com/api/?name=A&background=10B981&color=fff',
    hasSeenTour: true,
    role: 'admin' as const
  }
];

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      isLoading: false,
      error: null,
      mockUsers,

      login: async (email, password) => {
        set({ isLoading: true, error: null });
        
        try {
          await new Promise(resolve => setTimeout(resolve, 800));
          
          const user = mockUsers.find(
            (u) => u.email === email && u.password === password
          );
          
          if (user) {
            const { password: _, ...userWithoutPassword } = user;
            set({
              user: userWithoutPassword,
              isAuthenticated: true,
              isLoading: false
            });
          } else {
            set({
              error: 'Invalid email or password',
              isLoading: false
            });
          }
        } catch (error) {
          set({
            error: 'An error occurred during login',
            isLoading: false
          });
        }
      },

      register: async (name, email, password) => {
        set({ isLoading: true, error: null });
        
        try {
          await new Promise(resolve => setTimeout(resolve, 800));
          
          const existingUser = mockUsers.find((u) => u.email === email);
          
          if (existingUser) {
            set({
              error: 'User with this email already exists',
              isLoading: false
            });
            return;
          }
          
          const newUser = {
            id: (mockUsers.length + 1).toString(),
            name,
            email,
            password,
            profilePicture: `https://ui-avatars.com/api/?name=${name.charAt(0)}&background=0D8ABC&color=fff`,
            hasSeenTour: false,
            role: 'user' as const
          };
          
          mockUsers.push(newUser);
          
          const { password: _, ...userWithoutPassword } = newUser;
          
          set({
            user: userWithoutPassword,
            isAuthenticated: true,
            isLoading: false
          });
        } catch (error) {
          set({
            error: 'An error occurred during registration',
            isLoading: false
          });
        }
      },

      logout: () => {
        set({
          user: null,
          isAuthenticated: false
        });
      },

      clearError: () => {
        set({ error: null });
      },

      completeTour: () => {
        set((state) => ({
          user: state.user ? { ...state.user, hasSeenTour: true } : null
        }));
      }
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ 
        user: state.user,
        isAuthenticated: state.isAuthenticated 
      })
    }
  )
);

/== courseStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface Course {
  id: string;
  title: string;
  description: string;
  image: string;
  rating: number;
  students: number;
  duration: string;
  level: string;
  features: string[];
  price: string;
  color: string;
  category?: string;
  progress?: number;
  totalProblems?: number;
  solvedProblems?: number;
  totalQuizzes?: number;
  solvedQuizzes?: number;
  firstLessonId?: string;
}

interface Challenge {
  id: string;
  title: string;
  description: string;
  difficulty: 'easy' | 'medium' | 'hard';
  xpPoints: number;
}

interface LeaderboardUser {
  id: string;
  name: string;
  xp: number;
  rank: number;
  avatar: string;
}

interface CourseState {
  enrolledCourses: Course[];
  recommendedCourses: Course[];
  allCourses: Course[];
  listCourses: Course[];
  dailyChallenge: Challenge | null;
  leaderboard: LeaderboardUser[];
  currentStreak: number;
  highestStreak: number;
  totalXp: number;
  solvedChallenges: number;
  enrollInCourse: (courseId: string) => void;
  updateCourseProgress: (courseId: string, progress: number) => void;
  getCourseById: (courseId: string) => Course | undefined;
}

const listCourses: Course[] = [
  {
    id: 'go-beginner',
    title: 'Go | Beginner',
    description: 'Start your Go programming journey with basic concepts and syntax.',
    image: 'https://images.unsplash.com/photo-1542831371-29b0f74f9713?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.9,
    students: 4500,
    duration: '3 weeks',
    level: 'Beginner',
    features: [
      'Basic syntax and data types',
      'Control structures',
      'Functions and packages',
      'Basic error handling',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-cyan-500',
    firstLessonId: 'basic-syntax'
  },
  {
    id: 'go-fundamentals',
    title: 'Go | Fundamentals',
    description: 'Master Go fundamentals including structs, interfaces, error handling, and basic web development.',
    image: 'https://images.unsplash.com/photo-1580894894513-541e068a3e2b?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.8,
    students: 3200,
    duration: '4 weeks',
    level: 'Fundamental',
    features: [
      'Structs and interfaces',
      'Error handling',
      'Goroutines basics',
      'Basic web development',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-cyan-600',
    firstLessonId: 'structs-interfaces'
  },
  {
    id: 'go-intermediate',
    title: 'Go | Intermediate',
    description: 'Take your Go skills to the next level with advanced concurrency, testing, and API development.',
    image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.7,
    students: 2400,
    duration: '6 weeks',
    level: 'Intermediate',
    features: [
      'Advanced concurrency',
      'Testing and benchmarking',
      'Database integration',
      'RESTful APIs',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-cyan-700',
    firstLessonId: 'advanced-concurrency'
  },
  {
    id: 'go-expert',
    title: 'Go | Expert',
    description: 'Become a Go expert with microservices, performance optimization, and advanced design patterns.',
    image: 'https://images.unsplash.com/photo-1504639725590-34d0984388bd?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.9,
    students: 1800,
    duration: '8 weeks',
    level: 'Expert',
    features: [
      'Microservices architecture',
      'Performance optimization',
      'Advanced patterns',
      'System design',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-cyan-800',
    firstLessonId: 'microservices-intro'
  },
  {
    id: 'python',
    title: 'Python for Beginners',
    description: 'Learn Python programming from scratch with hands-on exercises and projects.',
    image: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.8,
    students: 12500,
    duration: '8 weeks',
    level: 'Beginner',
    features: [
      'Comprehensive Python fundamentals',
      'Interactive coding exercises',
      'Real-world projects',
      'Personalized feedback',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-blue-600',
    firstLessonId: 'python-basics'
  },
  {
    id: 'javascript',
    title: 'JavaScript Fundamentals',
    description: 'Master JavaScript basics and build interactive web applications.',
    image: 'https://images.unsplash.com/photo-1579468118864-1b9ea3c0db4a?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.7,
    students: 10800,
    duration: '6 weeks',
    level: 'Beginner',
    features: [
      'JavaScript core concepts',
      'DOM manipulation',
      'Event handling',
      'Interactive web projects',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-yellow-500',
    firstLessonId: 'js-intro'
  },
  {
    id: 'dsa',
    title: 'Data Structures & Algorithms',
    description: 'Learn essential DSA concepts to ace coding interviews and become a better programmer.',
    image: 'https://images.unsplash.com/photo-1516259762381-22954d7d3ad2?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.9,
    students: 8700,
    duration: '10 weeks',
    level: 'Intermediate',
    features: [
      'Comprehensive DSA coverage',
      'Problem-solving techniques',
      'Coding interview preparation',
      'Performance optimization',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-indigo-600',
    firstLessonId: 'arrays-intro'
  },
  {
    id: 'java',
    title: 'Java Programming',
    description: 'Learn Java programming from basics to advanced concepts with practical examples.',
    image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.6,
    students: 9200,
    duration: '8 weeks',
    level: 'Beginner to Intermediate',
    features: [
      'Java fundamentals and OOP',
      'Java collections framework',
      'Exception handling',
      'File I/O operations',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-red-600',
    firstLessonId: 'java-basics'
  },
  {
    id: 'cpp',
    title: 'C++ Programming',
    description: 'Master C++ programming with comprehensive tutorials and practical projects.',
    image: 'https://images.unsplash.com/photo-1542831371-29b0f74f9713?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.7,
    students: 7500,
    duration: '9 weeks',
    level: 'Intermediate',
    features: [
      'C++ fundamentals and OOP',
      'Memory management',
      'STL library',
      'Game development basics',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-green-600',
    firstLessonId: 'cpp-intro'
  },
  {
    id: 'web-dev',
    title: 'Full Stack Web Development',
    description: 'Learn to build complete web applications with modern technologies.',
    image: 'https://images.unsplash.com/photo-1547658719-da2b51169166?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
    rating: 4.8,
    students: 11300,
    duration: '12 weeks',
    level: 'Intermediate to Advanced',
    features: [
      'HTML, CSS, and JavaScript',
      'React.js frontend',
      'Node.js backend',
      'Database integration',
      'Certificate of completion',
    ],
    price: 'Free',
    color: 'bg-purple-600',
    firstLessonId: 'html-basics'
  }
];

const courses: Course[] = listCourses;

export const useCourseStore = create<CourseState>()(
  persist(
    (set, get) => ({
      enrolledCourses: [
        {
          id: 'c-programming',
          title: 'Learn C Programming',
          category: 'Programming',
          progress: 7,
          image: 'https://images.unsplash.com/photo-1515879218367-8466d910aaa4?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
          color: 'bg-orange-100',
          totalProblems: 64,
          solvedProblems: 0,
          totalQuizzes: 82,
          solvedQuizzes: 0,
          firstLessonId: 'c-intro'
        }
      ],
      recommendedCourses: [
        {
          id: 'go-beginner',
          title: 'Go | Beginner',
          category: 'Programming',
          progress: 0,
          image: 'https://images.unsplash.com/photo-1542831371-29b0f74f9713?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
          color: 'bg-cyan-50',
          totalProblems: 90,
          solvedProblems: 0,
          totalQuizzes: 60,
          solvedQuizzes: 0,
          firstLessonId: 'basic-syntax'
        },
        {
          id: 'go-fundamentals',
          title: 'Go | Fundamentals',
          category: 'Programming',
          progress: 0,
          image: 'https://images.unsplash.com/photo-1580894894513-541e068a3e2b?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
          color: 'bg-cyan-100',
          totalProblems: 120,
          solvedProblems: 0,
          totalQuizzes: 80,
          solvedQuizzes: 0,
          firstLessonId: 'structs-interfaces'
        },
        {
          id: 'go-intermediate',
          title: 'Go | Intermediate',
          category: 'Programming',
          progress: 0,
          image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=60',
          color: 'bg-cyan-200',
          totalProblems: 150,
          solvedProblems: 0,
          totalQuizzes: 100,
          solvedQuizzes: 0,
          firstLessonId: 'advanced-concurrency'
        }
      ],
      allCourses: courses,
      listCourses,
      dailyChallenge: {
        id: 'challenge-1',
        title: 'Convert to snake_case',
        description: 'Convert a camelCase string to snake_case',
        difficulty: 'easy',
        xpPoints: 50
      },
      leaderboard: [
        {
          id: '1',
          name: 'Mohammad Mahmud',
          xp: 3540,
          rank: 1,
          avatar: 'https://ui-avatars.com/api/?name=MM&background=0D8ABC&color=fff'
        },
        {
          id: '2',
          name: 'Johnnie Eye',
          xp: 3280,
          rank: 2,
          avatar: 'https://ui-avatars.com/api/?name=JE&background=9C27B0&color=fff'
        },
        {
          id: '3',
          name: 'Sabira Kabir Sabah',
          xp: 3150,
          rank: 3,
          avatar: 'https://ui-avatars.com/api/?name=SK&background=2196F3&color=fff'
        }
      ],
      currentStreak: 1,
      highestStreak: 1,
      totalXp: 0,
      solvedChallenges: 0,
      
      enrollInCourse: (courseId) => {
        set((state) => {
          const course = state.recommendedCourses.find(c => c.id === courseId);
          if (!course) return state;
          
          return {
            enrolledCourses: [...state.enrolledCourses, course],
            recommendedCourses: state.recommendedCourses.filter(c => c.id !== courseId)
          };
        });
      },
      
      updateCourseProgress: (courseId, progress) => {
        set((state) => ({
          enrolledCourses: state.enrolledCourses.map(course => 
            course.id === courseId ? { ...course, progress } : course
          )
        }));
      },

      getCourseById: (courseId) => {
        return listCourses.find(course => course.id === courseId);
      }
    }),
    {
      name: 'course-storage',
      partialize: (state) => ({ 
        enrolledCourses: state.enrolledCourses,
        totalXp: state.totalXp,
        solvedChallenges: state.solvedChallenges,
        currentStreak: state.currentStreak,
        highestStreak: state.highestStreak
      })
    }
  )
);

/== integrationStore.ts
import { create } from 'zustand';

interface ApiKey {
  id: string;
  name: string;
  key: string;
  created: string;
  lastUsed: string;
  requests: number;
}

interface PaymentGateway {
  id: string;
  name: string;
  status: 'active' | 'inactive';
  mode: 'live' | 'test';
  lastSync: string;
  transactionsCount: number;
}

interface SsoProvider {
  id: string;
  name: string;
  domain: string;
  status: 'active' | 'inactive';
  users: number;
  lastSync: string;
}

interface ThirdPartyIntegration {
  id: string;
  name: string;
  category: string;
  status: 'active' | 'inactive';
  lastSync: string;
  apiCalls: number;
}

interface Webhook {
  id: string;
  name: string;
  url: string;
  event: string;
  status: 'active' | 'inactive';
  lastTriggered: string;
  successRate: number;
}

interface IntegrationState {
  apiKeys: ApiKey[];
  paymentGateways: PaymentGateway[];
  ssoProviders: SsoProvider[];
  thirdPartyIntegrations: ThirdPartyIntegration[];
  webhooks: Webhook[];
  isLoading: boolean;
  error: string | null;
  
  // API Keys actions
  fetchApiKeys: () => Promise<void>;
  addApiKey: (apiKey: Omit<ApiKey, 'id'>) => Promise<void>;
  updateApiKey: (id: string, apiKey: Partial<ApiKey>) => Promise<void>;
  deleteApiKey: (id: string) => Promise<void>;
  
  // Payment Gateways actions
  fetchPaymentGateways: () => Promise<void>;
  addPaymentGateway: (gateway: Omit<PaymentGateway, 'id'>) => Promise<void>;
  updatePaymentGateway: (id: string, gateway: Partial<PaymentGateway>) => Promise<void>;
  deletePaymentGateway: (id: string) => Promise<void>;
  
  // SSO Providers actions
  fetchSsoProviders: () => Promise<void>;
  addSsoProvider: (provider: Omit<SsoProvider, 'id'>) => Promise<void>;
  updateSsoProvider: (id: string, provider: Partial<SsoProvider>) => Promise<void>;
  deleteSsoProvider: (id: string) => Promise<void>;
  
  // Third Party Integrations actions
  fetchThirdPartyIntegrations: () => Promise<void>;
  addThirdPartyIntegration: (integration: Omit<ThirdPartyIntegration, 'id'>) => Promise<void>;
  updateThirdPartyIntegration: (id: string, integration: Partial<ThirdPartyIntegration>) => Promise<void>;
  deleteThirdPartyIntegration: (id: string) => Promise<void>;
  
  // Webhooks actions
  fetchWebhooks: () => Promise<void>;
  addWebhook: (webhook: Omit<Webhook, 'id'>) => Promise<void>;
  updateWebhook: (id: string, webhook: Partial<Webhook>) => Promise<void>;
  deleteWebhook: (id: string) => Promise<void>;
}

// Mock data untuk pengembangan
const mockApiKeys: ApiKey[] = [
  {
    id: '1',
    name: 'Production API Key',
    key: 'pk_live_123456789',
    created: '2024-03-01',
    lastUsed: '2024-03-20 14:30',
    requests: 1256
  },
  {
    id: '2',
    name: 'Development API Key',
    key: 'pk_test_987654321',
    created: '2024-03-15',
    lastUsed: '2024-03-19 10:15',
    requests: 892
  }
];

const mockPaymentGateways: PaymentGateway[] = [
  {
    id: '1',
    name: 'Stripe',
    status: 'active',
    mode: 'live',
    lastSync: '2024-03-20 14:30',
    transactionsCount: 156
  },
  {
    id: '2',
    name: 'PayPal',
    status: 'inactive',
    mode: 'test',
    lastSync: '2024-03-19 10:15',
    transactionsCount: 89
  }
];

const mockSsoProviders: SsoProvider[] = [
  {
    id: '1',
    name: 'Google Workspace',
    domain: 'company.com',
    status: 'active',
    users: 156,
    lastSync: '2024-03-20 14:30'
  },
  {
    id: '2',
    name: 'Microsoft Azure AD',
    domain: 'enterprise.com',
    status: 'inactive',
    users: 89,
    lastSync: '2024-03-19 10:15'
  },
  {
    id: '3',
    name: 'Okta',
    domain: 'organization.com',
    status: 'active',
    users: 245,
    lastSync: '2024-03-18 09:45'
  }
];

const mockThirdPartyIntegrations: ThirdPartyIntegration[] = [
  {
    id: '1',
    name: 'Google Analytics',
    category: 'Analytics',
    status: 'active',
    lastSync: '2024-03-20 14:30',
    apiCalls: 1256
  },
  {
    id: '2',
    name: 'Mailchimp',
    category: 'Email',
    status: 'active',
    lastSync: '2024-03-19 10:15',
    apiCalls: 892
  },
  {
    id: '3',
    name: 'Slack',
    category: 'Communication',
    status: 'inactive',
    lastSync: '2024-03-18 09:45',
    apiCalls: 456
  }
];

const mockWebhooks: Webhook[] = [
  {
    id: '1',
    name: 'Course Completion Webhook',
    url: 'https://api.example.com/webhooks/course-complete',
    event: 'course.completed',
    status: 'active',
    lastTriggered: '2024-03-20 14:30',
    successRate: 98.5
  },
  {
    id: '2',
    name: 'Payment Success Webhook',
    url: 'https://api.example.com/webhooks/payment',
    event: 'payment.success',
    status: 'active',
    lastTriggered: '2024-03-19 10:15',
    successRate: 100
  },
  {
    id: '3',
    name: 'User Registration Webhook',
    url: 'https://api.example.com/webhooks/user',
    event: 'user.created',
    status: 'inactive',
    lastTriggered: '2024-03-18 09:45',
    successRate: 95.2
  }
];

// Implementasi store dengan Zustand
const useIntegrationStore = create<IntegrationState>((set, get) => ({
  apiKeys: mockApiKeys,
  paymentGateways: mockPaymentGateways,
  ssoProviders: mockSsoProviders,
  thirdPartyIntegrations: mockThirdPartyIntegrations,
  webhooks: mockWebhooks,
  isLoading: false,
  error: null,
  
  // API Keys actions
  fetchApiKeys: async () => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set({ apiKeys: mockApiKeys, isLoading: false });
    } catch (error) {
      set({ error: 'Gagal mengambil data API keys', isLoading: false });
    }
  },
  
  addApiKey: async (apiKey) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      const newApiKey = {
        ...apiKey,
        id: Date.now().toString(),
        created: new Date().toISOString().split('T')[0],
        lastUsed: '-',
        requests: 0
      };
      set(state => ({
        apiKeys: [...state.apiKeys, newApiKey],
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menambahkan API key', isLoading: false });
    }
  },
  
  updateApiKey: async (id, apiKey) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        apiKeys: state.apiKeys.map(key => key.id === id ? { ...key, ...apiKey } : key),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal memperbarui API key', isLoading: false });
    }
  },
  
  deleteApiKey: async (id) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        apiKeys: state.apiKeys.filter(key => key.id !== id),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menghapus API key', isLoading: false });
    }
  },
  
  // Payment Gateways actions
  fetchPaymentGateways: async () => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set({ paymentGateways: mockPaymentGateways, isLoading: false });
    } catch (error) {
      set({ error: 'Gagal mengambil data gateway pembayaran', isLoading: false });
    }
  },
  
  addPaymentGateway: async (gateway) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      const newGateway = {
        ...gateway,
        id: Date.now().toString(),
        lastSync: '-',
        transactionsCount: 0
      };
      set(state => ({
        paymentGateways: [...state.paymentGateways, newGateway],
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menambahkan gateway pembayaran', isLoading: false });
    }
  },
  
  updatePaymentGateway: async (id, gateway) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        paymentGateways: state.paymentGateways.map(g => g.id === id ? { ...g, ...gateway } : g),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal memperbarui gateway pembayaran', isLoading: false });
    }
  },
  
  deletePaymentGateway: async (id) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        paymentGateways: state.paymentGateways.filter(g => g.id !== id),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menghapus gateway pembayaran', isLoading: false });
    }
  },
  
  // SSO Providers actions
  fetchSsoProviders: async () => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set({ ssoProviders: mockSsoProviders, isLoading: false });
    } catch (error) {
      set({ error: 'Gagal mengambil data provider SSO', isLoading: false });
    }
  },
  
  addSsoProvider: async (provider) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      const newProvider = {
        ...provider,
        id: Date.now().toString(),
        lastSync: '-',
        users: 0
      };
      set(state => ({
        ssoProviders: [...state.ssoProviders, newProvider],
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menambahkan provider SSO', isLoading: false });
    }
  },
  
  updateSsoProvider: async (id, provider) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        ssoProviders: state.ssoProviders.map(p => p.id === id ? { ...p, ...provider } : p),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal memperbarui provider SSO', isLoading: false });
    }
  },
  
  deleteSsoProvider: async (id) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        ssoProviders: state.ssoProviders.filter(p => p.id !== id),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menghapus provider SSO', isLoading: false });
    }
  },
  
  // Third Party Integrations actions
  fetchThirdPartyIntegrations: async () => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set({ thirdPartyIntegrations: mockThirdPartyIntegrations, isLoading: false });
    } catch (error) {
      set({ error: 'Gagal mengambil data integrasi pihak ketiga', isLoading: false });
    }
  },
  
  addThirdPartyIntegration: async (integration) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      const newIntegration = {
        ...integration,
        id: Date.now().toString(),
        lastSync: '-',
        apiCalls: 0
      };
      set(state => ({
        thirdPartyIntegrations: [...state.thirdPartyIntegrations, newIntegration],
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menambahkan integrasi pihak ketiga', isLoading: false });
    }
  },
  
  updateThirdPartyIntegration: async (id, integration) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        thirdPartyIntegrations: state.thirdPartyIntegrations.map(i => i.id === id ? { ...i, ...integration } : i),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal memperbarui integrasi pihak ketiga', isLoading: false });
    }
  },
  
  deleteThirdPartyIntegration: async (id) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        thirdPartyIntegrations: state.thirdPartyIntegrations.filter(i => i.id !== id),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menghapus integrasi pihak ketiga', isLoading: false });
    }
  },
  
  // Webhooks actions
  fetchWebhooks: async () => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set({ webhooks: mockWebhooks, isLoading: false });
    } catch (error) {
      set({ error: 'Gagal mengambil data webhook', isLoading: false });
    }
  },
  
  addWebhook: async (webhook) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      const newWebhook = {
        ...webhook,
        id: Date.now().toString(),
        lastTriggered: '-',
        successRate: 0
      };
      set(state => ({
        webhooks: [...state.webhooks, newWebhook],
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menambahkan webhook', isLoading: false });
    }
  },
  
  updateWebhook: async (id, webhook) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        webhooks: state.webhooks.map(w => w.id === id ? { ...w, ...webhook } : w),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal memperbarui webhook', isLoading: false });
    }
  },
  
  deleteWebhook: async (id) => {
    set({ isLoading: true, error: null });
    try {
      // Simulasi API call
      await new Promise(resolve => setTimeout(resolve, 500));
      set(state => ({
        webhooks: state.webhooks.filter(w => w.id !== id),
        isLoading: false
      }));
    } catch (error) {
      set({ error: 'Gagal menghapus webhook', isLoading: false });
    }
  }
}));

export default useIntegrationStore;

/== learningStore.ts
import { create } from 'zustand';
import goCourses from '../data/courses/go';

export interface LearningStep {
  id: string;
  title: string;
  type: 'lesson' | 'quiz' | 'practice';
  content: string;
  code?: string;
  expectedOutput?: string;
  options?: {
    id: string;
    text: string;
    isCorrect: boolean;
  }[];
}

interface LessonContent {
  id: string;
  level: string;
  title: string;
  description: string;
  steps: LearningStep[];
  nextLessonId?: string;
}

interface CourseContent {
  id: string;
  title: string;
  description: string;
  lessons: LessonContent[];
}

interface LearningState {
  currentStep: number;
  setCurrentStep: (step: number) => void;
  getCurrentContent: (courseId: string, lessonId: string) => LessonContent | null;
}

// Course content mapping
const courseContent: Record<string, CourseContent> = {
  'go-beginner': {
    id: 'go-beginner',
    title: 'Go | Beginner',
    description: 'Start your Go programming journey with basic concepts and syntax.',
    lessons: goCourses.beginner.lessons
  },
  'go-fundamentals': {
    id: 'go-fundamentals',
    title: 'Go | Fundamentals',
    description: 'Master Go fundamentals including structs, interfaces, error handling, and basic web development',
    lessons: goCourses.fundamentals.lessons
  },
  'go-intermediate': {
    id: 'go-intermediate',
    title: 'Go | Intermediate',
    description: 'Take your Go skills to the next level with advanced concurrency, testing, and API development',
    lessons: goCourses.intermediate.lessons
  },
  'go-expert': {
    id: 'go-expert',
    title: 'Go | Expert',
    description: 'Become a Go expert with microservices, performance optimization, and advanced design patterns',
    lessons: goCourses.expert.lessons
  },
  'python': {
    id: 'python',
    title: 'Python',
    description: 'Learn Python programming',
    lessons: [
      {
        id: 'basic-syntax',
        level: 'basic-syntax',
        title: 'Python Basic Syntax',
        description: 'Learn the fundamental syntax and concepts of Python programming',
        steps: [
          {
            id: 'introduction',
            title: 'Introduction to Python',
            type: 'lesson',
            content: '<h2>Welcome to Python</h2><p>Python is a high-level, interpreted programming language...</p>',
            code: 'print("Hello, Python!")'
          }
        ]
      }
    ]
  },
  'javascript': {
    id: 'javascript',
    title: 'JavaScript',
    description: 'Learn JavaScript programming',
    lessons: [
      {
        id: 'basic-syntax',
        level: 'basic-syntax',
        title: 'JavaScript Basic Syntax',
        description: 'Learn the fundamental syntax and concepts of JavaScript',
        steps: [
          {
            id: 'introduction',
            title: 'Introduction to JavaScript',
            type: 'lesson',
            content: '<h2>Welcome to JavaScript</h2><p>JavaScript is a versatile programming language...</p>',
            code: 'console.log("Hello, JavaScript!");'
          }
        ]
      }
    ]
  }
};

export const useLearningStore = create<LearningState>((set) => ({
  currentStep: 1,

  setCurrentStep: (step) => {
    set({ currentStep: step });
  },

  getCurrentContent: (courseId, lessonId) => {
    const course = courseContent[courseId];
    if (!course) {
      return null;
    }

    // Ensure lessons is an array before using find
    if (!Array.isArray(course.lessons)) {
      return null;
    }

    const lesson = course.lessons.find(l => l.level === lessonId);
    if (!lesson) {
      return null;
    }

    return lesson;
  },
}));

/== notificationStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

export interface Notification {
  id: string;
  title: string;
  message: string;
  type: 'info' | 'success' | 'warning' | 'error';
  timestamp: number;
  read: boolean;
  link?: string;
}

interface NotificationState {
  notifications: Notification[];
  unreadCount: number;
  addNotification: (notification: Omit<Notification, 'id' | 'timestamp' | 'read'>) => void;
  markAsRead: (id: string) => void;
  markAllAsRead: () => void;
  removeNotification: (id: string) => void;
  clearAll: () => void;
}

const now = Date.now();
const hour = 3600000;
const day = 86400000;

export const useNotificationStore = create<NotificationState>()(
  persist(
    (set, get) => ({
      notifications: [
        {
          id: '1',
          title: 'New Course Available',
          message: 'Advanced Go Programming course is now available! Start learning advanced concepts and patterns.',
          type: 'info',
          timestamp: now - (1 * hour),
          read: false,
          link: '/courses/go-advanced'
        },
        {
          id: '2',
          title: 'Achievement Unlocked',
          message: 'Congratulations! You\'ve completed your first Go lesson. Keep up the great work!',
          type: 'success',
          timestamp: now - (2 * hour),
          read: false,
          link: '/achievements'
        },
        {
          id: '3',
          title: 'Daily Streak',
          message: 'Keep going! You\'re on a 3-day learning streak. Complete today\'s challenge to maintain it.',
          type: 'warning',
          timestamp: now - (1 * day),
          read: true
        },
        {
          id: '4',
          title: 'New Learning Path Available',
          message: 'Explore our new Backend Development learning path with structured curriculum.',
          type: 'info',
          timestamp: now - (2 * day),
          read: false,
          link: '/roadmap/backend'
        },
        {
          id: '5',
          title: 'Certificate Ready',
          message: 'Your Python Basics certificate is ready to be claimed! View and share your achievement.',
          type: 'success',
          timestamp: now - (3 * day),
          read: false,
          link: '/certificates'
        },
        {
          id: '6',
          title: 'Weekly Challenge',
          message: 'New weekly coding challenge is available. Solve it to earn bonus XP!',
          type: 'info',
          timestamp: now - (4 * day),
          read: true,
          link: '/challenges'
        },
        {
          id: '7',
          title: 'Community Milestone',
          message: 'Your helpful comments have reached 100 likes! Keep contributing to the community.',
          type: 'success',
          timestamp: now - (5 * day),
          read: true
        },
        {
          id: '8',
          title: 'Course Update',
          message: 'JavaScript Fundamentals course has been updated with new content and exercises.',
          type: 'info',
          timestamp: now - (6 * day),
          read: true,
          link: '/courses/javascript'
        },
        {
          id: '9',
          title: 'Security Alert',
          message: 'New login detected from an unknown device. Please review your account activity.',
          type: 'error',
          timestamp: now - (7 * day),
          read: true,
          link: '/settings/security'
        },
        {
          id: '10',
          title: 'Limited Time Offer',
          message: 'Get 50% off on annual subscription. Offer ends in 48 hours!',
          type: 'warning',
          timestamp: now - (8 * day),
          read: true,
          link: '/pricing'
        },
        {
          id: '11',
          title: 'Project Review',
          message: 'Your project submission has been reviewed. Check the feedback from our mentors.',
          type: 'info',
          timestamp: now - (9 * day),
          read: true,
          link: '/projects'
        },
        {
          id: '12',
          title: 'New Feature',
          message: 'Try out our new AI-powered code review feature in the compiler.',
          type: 'info',
          timestamp: now - (10 * day),
          read: true,
          link: '/compiler'
        },
        {
          id: '13',
          title: 'Learning Reminder',
          message: 'Don\'t forget to complete your daily coding practice to maintain your streak!',
          type: 'warning',
          timestamp: now - (11 * day),
          read: true
        },
        {
          id: '14',
          title: 'Community Event',
          message: 'Join our upcoming live coding session with industry experts this weekend.',
          type: 'info',
          timestamp: now - (12 * day),
          read: true,
          link: '/events'
        },
        {
          id: '15',
          title: 'Profile Milestone',
          message: 'Your profile has been viewed 1000 times! Update your portfolio to attract more opportunities.',
          type: 'success',
          timestamp: now - (13 * day),
          read: true,
          link: '/profile'
        }
      ],
      unreadCount: 4,

      addNotification: (notification) => {
        const newNotification = {
          ...notification,
          id: Math.random().toString(36).substr(2, 9),
          timestamp: Date.now(),
          read: false
        };

        set((state) => ({
          notifications: [newNotification, ...state.notifications],
          unreadCount: state.unreadCount + 1
        }));
      },

      markAsRead: (id) => {
        set((state) => {
          const notification = state.notifications.find(n => n.id === id);
          if (notification && !notification.read) {
            return {
              notifications: state.notifications.map(n =>
                n.id === id ? { ...n, read: true } : n
              ),
              unreadCount: state.unreadCount - 1
            };
          }
          return state;
        });
      },

      markAllAsRead: () => {
        set((state) => ({
          notifications: state.notifications.map(n => ({ ...n, read: true })),
          unreadCount: 0
        }));
      },

      removeNotification: (id) => {
        set((state) => {
          const notification = state.notifications.find(n => n.id === id);
          return {
            notifications: state.notifications.filter(n => n.id !== id),
            unreadCount: notification && !notification.read 
              ? state.unreadCount - 1 
              : state.unreadCount
          };
        });
      },

      clearAll: () => {
        set({ notifications: [], unreadCount: 0 });
      }
    }),
    {
      name: 'notification-storage'
    }
  )
);

/== roadmapStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

export interface RoadmapNode {
  id: string;
  title: string;
  description?: string;
  type: 'primary' | 'secondary' | 'highlight';
  isCompleted?: boolean;
  isActive?: boolean;
  children?: RoadmapNode[];
  parentId?: string;
}

interface RoadmapState {
  currentRoadmap: string | null;
  completedNodes: string[];
  activeNode: string | null;
  setCurrentRoadmap: (roadmap: string) => void;
  completeNode: (nodeId: string) => void;
  setActiveNode: (nodeId: string | null) => void;
  getRoadmapProgress: (roadmapId: string) => number;
}

export const useRoadmapStore = create<RoadmapState>()(
  persist(
    (set, get) => ({
      currentRoadmap: null,
      completedNodes: [],
      activeNode: null,

      setCurrentRoadmap: (roadmap) => {
        set({ currentRoadmap: roadmap });
      },

      completeNode: (nodeId) => {
        set((state) => ({
          completedNodes: [...state.completedNodes, nodeId]
        }));
      },

      setActiveNode: (nodeId) => {
        set({ activeNode: nodeId });
      },

      getRoadmapProgress: (roadmapId) => {
        const { completedNodes } = get();
        // Calculate progress based on completed nodes
        // This is a simplified version - you would need to know total nodes for the roadmap
        return (completedNodes.length / 100) * 100;
      }
    }),
    {
      name: 'roadmap-storage'
    }
  )
);

/== submissionStore.ts
import { create } from 'zustand';

export interface SubmissionState {
  status: 'reviewing' | 'rejected' | 'approved' | null;
  feedback: {
    type: 'success' | 'error';
    message: string;
  }[];
  reviewerNotes: string | null;
  submissionFiles: {
    name: string;
    path: string;
    comments: number;
  }[];
  setStatus: (status: 'reviewing' | 'rejected' | 'approved' | null) => void;
  setFeedback: (feedback: { type: 'success' | 'error'; message: string; }[]) => void;
  setReviewerNotes: (notes: string | null) => void;
  setSubmissionFiles: (files: { name: string; path: string; comments: number; }[]) => void;
  reset: () => void;
}

export const useSubmissionStore = create<SubmissionState>((set) => ({
  status: null,
  feedback: [],
  reviewerNotes: null,
  submissionFiles: [],
  
  setStatus: (status) => set({ status }),
  setFeedback: (feedback) => set({ feedback }),
  setReviewerNotes: (notes) => set({ reviewerNotes: notes }),
  setSubmissionFiles: (files) => set({ submissionFiles: files }),
  reset: () => set({
    status: null,
    feedback: [],
    reviewerNotes: null,
    submissionFiles: []
  })
}));

/== subscriptionStore.ts
import { create } from 'zustand';

interface Feature {
  name: string;
  included: boolean;
}

export interface Subscription {
  id: string;
  type: 'free' | 'premium' | 'lifetime' | string;
  title: string;
  subtitle: string;
  monthlyPrice: number;
  yearlyPrice: number;
  buttonText: string;
  features: Feature[];
  isPopular?: boolean;
  guarantee?: string;
}

// Updated interface for global settings
export interface SubscriptionSettings {
  discountText: string;
  regionText: string;
  yearlyButtonText: string;
  monthlyButtonText: string;
  isMonthly: boolean; // Added to track current view mode
}

// Enhanced interface to match the updated SubscriptionCard editedValues format
interface CardEditedValues {
  title: string;
  subtitle: string;
  price: number;
  buttonText: string;
  features: Feature[];
  newFeature?: string; // Optional - only used in the card component
  guarantee?: string; // Added guarantee field
}

interface SubscriptionStore {
  subscriptions: Subscription[];
  settings: SubscriptionSettings;
  addSubscription: (subscription: Subscription) => void;
  updateSubscription: (id: string, updatedData: Partial<Subscription>) => void;
  updateSubscriptionFromCard: (id: string, editedValues: CardEditedValues) => void;
  addFeature: (id: string, featureName: string) => void;
  deleteFeature: (id: string, featureIndex: number) => void;
  updateFeature: (id: string, featureIndex: number, newName: string) => void;
  updateGuarantee: (id: string, guaranteeText: string) => void; // Added method for guarantee updates
  deleteSubscription: (subscriptionId: string) => void;
  updateSettings: (newSettings: Partial<SubscriptionSettings>) => void;
  togglePriceMode: () => void; // Toggle between monthly and yearly
  initializeStore: () => void;
  getCurrentPrice: (subscription: Subscription) => number; // Helper to get current price based on view mode
}

// Sample data matching the image shown
const sampleSubscriptions: Subscription[] = [
  {
    id: '1',
    type: 'free',
    title: 'Free',
    subtitle: 'Remains free forever',
    monthlyPrice: 0,
    yearlyPrice: 0,
    buttonText: 'Start for free',
    isPopular: false,
    features: [
      { name: 'Early chapters', included: true },
      { name: 'Limited challenges', included: true },
      { name: 'Unlimited AI guidance', included: true },
      { name: 'Practice projects', included: true },
      { name: 'Professional certifications', included: true },
      { name: 'Unlimited coding playground', included: true },
    ]
  },
  {
    id: '2',
    type: 'premium',
    title: 'Premium',
    subtitle: 'Access to everything',
    monthlyPrice: 48000, // Monthly price
    yearlyPrice: 578317, // Yearly price (shown in the image)
    buttonText: 'Get premium',
    isPopular: true,
    features: [
      { name: 'Full course catalog', included: true },
      { name: 'Coding challenges', included: true },
      { name: 'Unlimited AI guidance', included: true },
      { name: 'Practice projects', included: true },
      { name: 'Interview preparation courses', included: true },
      { name: 'Professional certifications', included: true },
      { name: 'Unlimited coding playground', included: true },
    ],
    guarantee: '14-day money back guarantee'
  },
  {
    id: '3',
    type: 'lifetime',
    title: 'Lifetime',
    subtitle: 'Access to PRO forever',
    monthlyPrice: 160000, // Estimated monthly equivalent
    yearlyPrice: 1923097, // Lifetime price (shown in the image)
    buttonText: 'Get lifetime access',
    isPopular: false,
    features: [
      { name: 'Everything in Premium plus:', included: true },
      { name: 'Lifetime access', included: true },
      { name: 'No recurring payments', included: true },
    ]
  }
];

// Updated default settings
const defaultSettings: SubscriptionSettings = {
  discountText: 'Save 66%',
  regionText: 'Price discounted based on your region',
  yearlyButtonText: 'Yearly',
  monthlyButtonText: 'Monthly',
  isMonthly: true // Default to monthly view
};

export const useSubscriptionStore = create<SubscriptionStore>((set, get) => ({
  subscriptions: [],
  settings: defaultSettings,
  
  addSubscription: (subscription) => 
    set((state) => ({ 
      subscriptions: [...state.subscriptions, subscription] 
    })),
  
  // Generic update method for direct updates
  updateSubscription: (id, updatedData) => 
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => 
        sub.id === id ? { ...sub, ...updatedData } : sub
      )
    })),
  
  // Method that handles updates coming from the SubscriptionCard component
  updateSubscriptionFromCard: (id, editedValues) => {
    const { settings } = get();
    
    // Map card values to subscription format
    const updatedData: Partial<Subscription> = {
      title: editedValues.title,
      subtitle: editedValues.subtitle,
      buttonText: editedValues.buttonText,
      features: editedValues.features,
      guarantee: editedValues.guarantee
    };
    
    // Update the correct price field based on current view mode
    if (settings.isMonthly) {
      updatedData.monthlyPrice = editedValues.price;
    } else {
      updatedData.yearlyPrice = editedValues.price;
    }
    
    // Update the subscription
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => 
        sub.id === id ? { ...sub, ...updatedData } : sub
      )
    }));
  },
  
  // Add a new feature to a subscription
  addFeature: (id, featureName) => 
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => 
        sub.id === id 
          ? { 
              ...sub, 
              features: [...sub.features, { name: featureName, included: true }]
            } 
          : sub
      )
    })),
  
  // Delete a feature from a subscription
  deleteFeature: (id, featureIndex) => 
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => {
        if (sub.id === id) {
          const updatedFeatures = [...sub.features];
          updatedFeatures.splice(featureIndex, 1);
          return { ...sub, features: updatedFeatures };
        }
        return sub;
      })
    })),
  
  // Update a feature name
  updateFeature: (id, featureIndex, newName) => 
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => {
        if (sub.id === id) {
          const updatedFeatures = [...sub.features];
          updatedFeatures[featureIndex] = { 
            ...updatedFeatures[featureIndex], 
            name: newName 
          };
          return { ...sub, features: updatedFeatures };
        }
        return sub;
      })
    })),
    
  // Update the guarantee text
  updateGuarantee: (id, guaranteeText) => 
    set((state) => ({
      subscriptions: state.subscriptions.map(sub => 
        sub.id === id 
          ? { ...sub, guarantee: guaranteeText } 
          : sub
      )
    })),
  
  deleteSubscription: (subscriptionId) => 
    set((state) => ({
      subscriptions: state.subscriptions.filter(sub => sub.id !== subscriptionId)
    })),
    
  // Updated to accept partial settings updates
  updateSettings: (newSettings) =>
    set((state) => ({
      settings: { ...state.settings, ...newSettings }
    })),
  
  // Toggle between monthly and yearly view
  togglePriceMode: () =>
    set((state) => ({
      settings: { 
        ...state.settings, 
        isMonthly: !state.settings.isMonthly 
      }
    })),

  // Helper to get the current price based on view mode
  getCurrentPrice: (subscription) => {
    const { settings } = get();
    return settings.isMonthly ? subscription.monthlyPrice : subscription.yearlyPrice;
  },

  // Function to initialize the store with sample data
  initializeStore: () => 
    set({ 
      subscriptions: sampleSubscriptions,
      settings: defaultSettings 
    })
}));

// Initialize the store when the module is imported
// Remove this if you want to call initializeStore explicitly in your app
useSubscriptionStore.getState().initializeStore();

/== themeStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface ThemeState {
  isDarkMode: boolean;
  toggleTheme: () => void;
}

export const useThemeStore = create<ThemeState>()(
  persist(
    (set) => ({
      isDarkMode: true, // Default to dark mode
      toggleTheme: () => set((state) => ({ isDarkMode: !state.isDarkMode })),
    }),
    {
      name: 'theme-storage',
    }
  )
);

/== useBundleStore.ts
import create from 'zustand';
import { devtools, persist } from 'zustand/middleware';

export interface Course {
  id: string;
  title: string;
  description: string;
  price: number;
  duration: number;
  level: 'beginner' | 'intermediate' | 'advanced';
  category: string;
  image: string;
}

export interface Bundle {
  id: string;
  title: string;
  description: string;
  courses: string[]; // array of course IDs
  originalPrice: number; // sum of individual course prices
  bundlePrice: number; // discounted price
  discountPercentage: number;
  theme: string;
  difficultyLevel: 'beginner' | 'intermediate' | 'advanced';
  careerPath?: string;
  limitedTime: boolean;
  startDate?: Date;
  endDate?: Date;
  accessDuration: 'lifetime' | number; // number of days if not lifetime
  prerequisites?: string[];
  courseOrder?: string[];
  hasCertificate: boolean;
  createdAt: Date;
  updatedAt: Date;
  isActive: boolean;
  customLandingPage?: {
    banner?: string;
    testimonials?: { name: string; text: string }[];
    highlights?: string[];
    customCSS?: string;
  };
}

export interface BundleStats {
  id: string;
  bundleId: string;
  purchases: number;
  completionRate: number;
  averageCompletionTime: number;
  mostPopularCourse: string;
  revenue: number;
  createdAt: Date;
}

interface BundleState {
  bundles: Bundle[];
  courses: Course[];
  stats: BundleStats[];
  selectedBundle: Bundle | null;
  isLoading: boolean;
  error: string | null;
  
  // Actions
  fetchBundles: () => Promise<void>;
  fetchCourses: () => Promise<void>;
  fetchBundleStats: (bundleId: string) => Promise<void>;
  createBundle: (bundle: Omit<Bundle, 'id' | 'createdAt' | 'updatedAt'>) => Promise<void>;
  updateBundle: (id: string, bundle: Partial<Bundle>) => Promise<void>;
  deleteBundle: (id: string) => Promise<void>;
  selectBundle: (id: string) => void;
  clearSelectedBundle: () => void;
}

// Sample Courses Data
const sampleCourses: Course[] = [
  {
    id: 'c1',
    title: 'React Fundamentals',
    description: 'Learn the basics of React.js and build your first application from scratch.',
    price: 599000,
    duration: 12, // in hours
    level: 'beginner',
    category: 'frontend',
    image: 'https://via.placeholder.com/300x200?text=React+Fundamentals'
  },
  {
    id: 'c2',
    title: 'Advanced React Patterns',
    description: 'Master advanced React patterns and state management techniques for large applications.',
    price: 799000,
    duration: 15,
    level: 'intermediate',
    category: 'frontend',
    image: 'https://via.placeholder.com/300x200?text=Advanced+React'
  },
  {
    id: 'c3',
    title: 'Node.js Backend Development',
    description: 'Build scalable backends using Node.js, Express, and MongoDB.',
    price: 699000,
    duration: 14,
    level: 'intermediate',
    category: 'backend',
    image: 'https://via.placeholder.com/300x200?text=Node.js+Backend'
  },
  {
    id: 'c4',
    title: 'Introduction to Python',
    description: 'Start your programming journey with Python, a versatile and beginner-friendly language.',
    price: 499000,
    duration: 10,
    level: 'beginner',
    category: 'programming',
    image: 'https://via.placeholder.com/300x200?text=Intro+to+Python'
  },
  {
    id: 'c5',
    title: 'Data Science with Python',
    description: 'Learn data analysis, visualization, and machine learning using Python libraries.',
    price: 899000,
    duration: 20,
    level: 'intermediate',
    category: 'data-science',
    image: 'https://via.placeholder.com/300x200?text=Data+Science'
  },
  {
    id: 'c6',
    title: 'Database Design & SQL',
    description: 'Master relational database design principles and SQL queries.',
    price: 599000,
    duration: 12,
    level: 'beginner',
    category: 'database',
    image: 'https://via.placeholder.com/300x200?text=SQL+Basics'
  },
  {
    id: 'c7',
    title: 'DevOps Engineering',
    description: 'Learn CI/CD pipelines, containerization, and cloud deployment strategies.',
    price: 999000,
    duration: 18,
    level: 'advanced',
    category: 'devops',
    image: 'https://via.placeholder.com/300x200?text=DevOps'
  },
  {
    id: 'c8',
    title: 'Mobile App Development with Flutter',
    description: 'Build cross-platform mobile apps for iOS and Android using Flutter and Dart.',
    price: 799000,
    duration: 16,
    level: 'intermediate',
    category: 'mobile',
    image: 'https://via.placeholder.com/300x200?text=Flutter'
  },
  {
    id: 'c9',
    title: 'Java Programming Masterclass',
    description: 'Comprehensive Java course covering basics to advanced concepts including Spring Boot.',
    price: 849000,
    duration: 25,
    level: 'intermediate',
    category: 'programming',
    image: 'https://via.placeholder.com/300x200?text=Java+Masterclass'
  },
  {
    id: 'c10',
    title: 'UI/UX Design Principles',
    description: 'Learn user interface and experience design fundamentals to create engaging digital products.',
    price: 649000,
    duration: 14,
    level: 'beginner',
    category: 'design',
    image: 'https://via.placeholder.com/300x200?text=UI/UX+Design'
  },
  {
    id: 'c11',
    title: 'Go Programming Language',
    description: 'Learn Go (Golang) for building efficient, reliable, and scalable server-side applications.',
    price: 749000,
    duration: 15,
    level: 'intermediate',
    category: 'programming',
    image: 'https://via.placeholder.com/300x200?text=Go+Programming'
  },
  {
    id: 'c12',
    title: 'Cyber Security Fundamentals',
    description: 'Understand security principles, threat models, and basic penetration testing techniques.',
    price: 849000,
    duration: 18,
    level: 'beginner',
    category: 'security',
    image: 'https://via.placeholder.com/300x200?text=Cyber+Security'
  },
  {
    id: 'c13',
    title: 'AWS Cloud Practitioner',
    description: 'Get started with Amazon Web Services and learn cloud fundamentals.',
    price: 599000,
    duration: 12,
    level: 'beginner',
    category: 'cloud',
    image: 'https://via.placeholder.com/300x200?text=AWS+Cloud'
  },
  {
    id: 'c14',
    title: 'Microservices Architecture',
    description: 'Design, implement and deploy microservices-based applications.',
    price: 899000,
    duration: 16,
    level: 'advanced',
    category: 'architecture',
    image: 'https://via.placeholder.com/300x200?text=Microservices'
  },
  {
    id: 'c15',
    title: 'Machine Learning Engineering',
    description: 'Apply machine learning algorithms to solve real-world problems at scale.',
    price: 1099000,
    duration: 24,
    level: 'advanced',
    category: 'data-science',
    image: 'https://via.placeholder.com/300x200?text=ML+Engineering'
  }
];

// Sample Bundles Data
const sampleBundles: Bundle[] = [
  {
    id: 'b1',
    title: 'Frontend Web Developer Path',
    description: 'Complete frontend development journey from fundamentals to advanced React patterns.',
    courses: ['c1', 'c2', 'c10'],
    originalPrice: 2047000,
    bundlePrice: 1599000,
    discountPercentage: 22,
    theme: 'Web Development',
    difficultyLevel: 'intermediate',
    careerPath: 'Frontend Developer',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['Basic HTML and CSS knowledge'],
    courseOrder: ['c1', 'c10', 'c2'],
    hasCertificate: true,
    createdAt: new Date('2024-03-10'),
    updatedAt: new Date('2024-04-01'),
    isActive: true,
    customLandingPage: {
      banner: 'https://via.placeholder.com/1200x300?text=Frontend+Developer+Path',
      testimonials: [
        { name: 'Sarah J.', text: 'This bundle transformed my career. I went from basic HTML knowledge to a full-time React developer in 4 months!' },
        { name: 'Michael T.', text: 'The course progression is perfect. Each course builds on the previous one naturally.' }
      ],
      highlights: ['Build 5 complete projects for your portfolio', '24/7 community access', 'Resume review included']
    }
  },
  {
    id: 'b2',
    title: 'Full-Stack JavaScript Developer',
    description: 'Master both frontend and backend development using JavaScript technologies.',
    courses: ['c1', 'c2', 'c3'],
    originalPrice: 2097000,
    bundlePrice: 1699000,
    discountPercentage: 19,
    theme: 'Web Development',
    difficultyLevel: 'intermediate',
    careerPath: 'Full-Stack Developer',
    limitedTime: false,
    accessDuration: 'lifetime',
    courseOrder: ['c1', 'c2', 'c3'],
    hasCertificate: true,
    createdAt: new Date('2024-02-15'),
    updatedAt: new Date('2024-04-05'),
    isActive: true
  },
  {
    id: 'b3',
    title: 'Data Science Bootcamp',
    description: 'Comprehensive data science program covering Python, statistics, and machine learning.',
    courses: ['c4', 'c5', 'c15'],
    originalPrice: 2497000,
    bundlePrice: 1899000,
    discountPercentage: 24,
    theme: 'Data Science',
    difficultyLevel: 'intermediate',
    careerPath: 'Data Scientist',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['Basic math knowledge', 'Problem-solving skills'],
    courseOrder: ['c4', 'c5', 'c15'],
    hasCertificate: true,
    createdAt: new Date('2024-01-20'),
    updatedAt: new Date('2024-03-15'),
    isActive: true
  },
  {
    id: 'b4',
    title: 'Mobile App Developer Bundle',
    description: 'Learn to build mobile applications for iOS and Android using Flutter.',
    courses: ['c8', 'c10'],
    originalPrice: 1448000,
    bundlePrice: 1199000,
    discountPercentage: 17,
    theme: 'Mobile Development',
    difficultyLevel: 'intermediate',
    careerPath: 'Mobile Developer',
    limitedTime: true,
    startDate: new Date('2024-04-01'),
    endDate: new Date('2024-06-30'),
    accessDuration: 365, // 1 year
    hasCertificate: true,
    createdAt: new Date('2024-03-25'),
    updatedAt: new Date('2024-03-25'),
    isActive: true
  },
  {
    id: 'b5',
    title: 'Cloud Engineering Certification',
    description: 'Prepare for cloud certifications and master deployment on major cloud platforms.',
    courses: ['c7', 'c13', 'c14'],
    originalPrice: 2497000,
    bundlePrice: 1999000,
    discountPercentage: 20,
    theme: 'Cloud Computing',
    difficultyLevel: 'advanced',
    careerPath: 'Cloud Engineer',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['Basic networking knowledge', 'Command line familiarity'],
    courseOrder: ['c13', 'c7', 'c14'],
    hasCertificate: true,
    createdAt: new Date('2024-02-01'),
    updatedAt: new Date('2024-04-10'),
    isActive: true
  },
  {
    id: 'b6',
    title: 'Programming Fundamentals',
    description: 'Build strong programming foundations with Python and Java.',
    courses: ['c4', 'c9'],
    originalPrice: 1348000,
    bundlePrice: 999000,
    discountPercentage: 26,
    theme: 'Programming',
    difficultyLevel: 'beginner',
    limitedTime: false,
    accessDuration: 'lifetime',
    courseOrder: ['c4', 'c9'],
    hasCertificate: true,
    createdAt: new Date('2024-01-05'),
    updatedAt: new Date('2024-03-20'),
    isActive: true
  },
  {
    id: 'b7',
    title: 'Backend Developer Path',
    description: 'Comprehensive backend development skills with multiple technologies.',
    courses: ['c3', 'c6', 'c11', 'c14'],
    originalPrice: 3146000,
    bundlePrice: 2499000,
    discountPercentage: 21,
    theme: 'Backend Development',
    difficultyLevel: 'advanced',
    careerPath: 'Backend Engineer',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['Basic programming knowledge'],
    courseOrder: ['c6', 'c3', 'c11', 'c14'],
    hasCertificate: true,
    createdAt: new Date('2024-02-10'),
    updatedAt: new Date('2024-04-05'),
    isActive: true
  },
  {
    id: 'b8',
    title: 'Cyber Security Specialist',
    description: 'Master cyber security principles and practices to protect digital assets.',
    courses: ['c12', 'c6', 'c7'],
    originalPrice: 2447000,
    bundlePrice: 1899000,
    discountPercentage: 22,
    theme: 'Cyber Security',
    difficultyLevel: 'intermediate',
    careerPath: 'Security Specialist',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['Basic IT knowledge'],
    courseOrder: ['c12', 'c6', 'c7'],
    hasCertificate: true,
    createdAt: new Date('2024-03-01'),
    updatedAt: new Date('2024-04-15'),
    isActive: true
  },
  {
    id: 'b9',
    title: 'Modern Web Technologies',
    description: 'Stay current with the latest web development technologies and practices.',
    courses: ['c1', 'c3', 'c7', 'c14'],
    originalPrice: 3296000,
    bundlePrice: 2599000,
    discountPercentage: 21,
    theme: 'Web Development',
    difficultyLevel: 'intermediate',
    limitedTime: true,
    startDate: new Date('2024-05-01'),
    endDate: new Date('2024-07-31'),
    accessDuration: 'lifetime',
    hasCertificate: true,
    createdAt: new Date('2024-04-01'),
    updatedAt: new Date('2024-04-01'),
    isActive: true
  },
  {
    id: 'b10',
    title: 'Enterprise Developer Bundle',
    description: 'Complete skill set for enterprise application development.',
    courses: ['c9', 'c6', 'c14', 'c7', 'c13'],
    originalPrice: 3895000,
    bundlePrice: 2999000,
    discountPercentage: 23,
    theme: 'Enterprise Development',
    difficultyLevel: 'advanced',
    careerPath: 'Enterprise Architect',
    limitedTime: false,
    accessDuration: 'lifetime',
    prerequisites: ['2+ years programming experience'],
    courseOrder: ['c9', 'c6', 'c14', 'c7', 'c13'],
    hasCertificate: true,
    createdAt: new Date('2024-01-15'),
    updatedAt: new Date('2024-03-25'),
    isActive: true,
    customLandingPage: {
      banner: 'https://via.placeholder.com/1200x300?text=Enterprise+Developer+Bundle',
      testimonials: [
        { name: 'Robert K.', text: 'This bundle filled the gaps in my enterprise development knowledge. Highly recommended for senior developers.' },
        { name: 'Priya M.', text: 'The microservices and cloud sections alone are worth the price. Transformative for my career.' }
      ],
      highlights: ['Industry-standard architecture patterns', 'Real-world enterprise projects', 'Direct mentor access']
    }
  }
];

// Sample Bundle Stats
const sampleBundleStats: BundleStats[] = [
  {
    id: 'bs1',
    bundleId: 'b1',
    purchases: 328,
    completionRate: 68,
    averageCompletionTime: 45, // days
    mostPopularCourse: 'c1',
    revenue: 524772000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs2',
    bundleId: 'b2',
    purchases: 245,
    completionRate: 62,
    averageCompletionTime: 60,
    mostPopularCourse: 'c1',
    revenue: 416255000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs3',
    bundleId: 'b3',
    purchases: 187,
    completionRate: 55,
    averageCompletionTime: 75,
    mostPopularCourse: 'c4',
    revenue: 355113000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs4',
    bundleId: 'b4',
    purchases: 129,
    completionRate: 72,
    averageCompletionTime: 30,
    mostPopularCourse: 'c8',
    revenue: 154671000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs5',
    bundleId: 'b5',
    purchases: 156,
    completionRate: 48,
    averageCompletionTime: 90,
    mostPopularCourse: 'c13',
    revenue: 311844000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs6',
    bundleId: 'b6',
    purchases: 412,
    completionRate: 78,
    averageCompletionTime: 35,
    mostPopularCourse: 'c4',
    revenue: 411588000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs7',
    bundleId: 'b7',
    purchases: 189,
    completionRate: 45,
    averageCompletionTime: 110,
    mostPopularCourse: 'c3',
    revenue: 472311000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs8',
    bundleId: 'b8',
    purchases: 142,
    completionRate: 58,
    averageCompletionTime: 65,
    mostPopularCourse: 'c12',
    revenue: 269658000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs9',
    bundleId: 'b9',
    purchases: 97,
    completionRate: 42,
    averageCompletionTime: 50,
    mostPopularCourse: 'c1',
    revenue: 252103000,
    createdAt: new Date('2024-04-15')
  },
  {
    id: 'bs10',
    bundleId: 'b10',
    purchases: 78,
    completionRate: 38,
    averageCompletionTime: 130,
    mostPopularCourse: 'c9',
    revenue: 233922000,
    createdAt: new Date('2024-04-15')
  }
];

// Mock API function (replace with actual API calls)
const api = {
  fetchBundles: async (): Promise<Bundle[]> => {
    // Return sample bundles instead of empty array
    return Promise.resolve(sampleBundles);
  },
  fetchCourses: async (): Promise<Course[]> => {
    // Return sample courses instead of empty array
    return Promise.resolve(sampleCourses);
  },
  fetchBundleStats: async (bundleId: string): Promise<BundleStats> => {
    // Return actual stats for the bundle if available
    const stats = sampleBundleStats.find(s => s.bundleId === bundleId);
    if (stats) {
      return Promise.resolve(stats);
    }
    
    // Return default stats if not found
    return Promise.resolve({
      id: Math.random().toString(36).substring(2, 9),
      bundleId,
      purchases: 0,
      completionRate: 0,
      averageCompletionTime: 0,
      mostPopularCourse: '',
      revenue: 0,
      createdAt: new Date(),
    });
  },
  createBundle: async (bundle: Omit<Bundle, 'id' | 'createdAt' | 'updatedAt'>): Promise<Bundle> => {
    // Simulate API call
    return Promise.resolve({
      ...bundle,
      id: Math.random().toString(36).substring(2, 9),
      createdAt: new Date(),
      updatedAt: new Date(),
    });
  },
  updateBundle: async (id: string, bundle: Partial<Bundle>): Promise<Bundle> => {
    // Simulate API call
    return Promise.resolve({
      ...bundle,
      id,
      updatedAt: new Date(),
    } as Bundle);
  },
  deleteBundle: async (id: string): Promise<void> => {
    // Simulate API call
    return Promise.resolve();
  },
};

const useBundleStore = create<BundleState>()(
  devtools(
    persist(
      (set, get) => ({
        // Initialize with sample data
        bundles: sampleBundles,
        courses: sampleCourses,
        stats: sampleBundleStats,
        selectedBundle: null,
        isLoading: false,
        error: null,
        
        fetchBundles: async () => {
          set({ isLoading: true, error: null });
          try {
            const bundles = await api.fetchBundles();
            set({ bundles, isLoading: false });
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        fetchCourses: async () => {
          set({ isLoading: true, error: null });
          try {
            const courses = await api.fetchCourses();
            set({ courses, isLoading: false });
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        fetchBundleStats: async (bundleId: string) => {
          set({ isLoading: true, error: null });
          try {
            const bundleStats = await api.fetchBundleStats(bundleId);
            set((state) => ({
              stats: [...state.stats.filter(s => s.bundleId !== bundleId), bundleStats],
              isLoading: false,
            }));
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        createBundle: async (bundle) => {
          set({ isLoading: true, error: null });
          try {
            const newBundle = await api.createBundle(bundle);
            set((state) => ({
              bundles: [...state.bundles, newBundle],
              isLoading: false,
            }));
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        updateBundle: async (id, bundle) => {
          set({ isLoading: true, error: null });
          try {
            const updatedBundle = await api.updateBundle(id, bundle);
            set((state) => ({
              bundles: state.bundles.map(b => b.id === id ? { ...b, ...updatedBundle } : b),
              selectedBundle: state.selectedBundle?.id === id ? { ...state.selectedBundle, ...updatedBundle } : state.selectedBundle,
              isLoading: false,
            }));
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        deleteBundle: async (id) => {
          set({ isLoading: true, error: null });
          try {
            await api.deleteBundle(id);
            set((state) => ({
              bundles: state.bundles.filter(b => b.id !== id),
              selectedBundle: state.selectedBundle?.id === id ? null : state.selectedBundle,
              isLoading: false,
            }));
          } catch (error) {
            set({ error: (error as Error).message, isLoading: false });
          }
        },
        
        selectBundle: (id) => {
          const { bundles } = get();
          const bundle = bundles.find(b => b.id === id) || null;
          set({ selectedBundle: bundle });
        },
        
        clearSelectedBundle: () => {
          set({ selectedBundle: null });
        },
      }),
      {
        name: 'bundle-storage',
      }
    )
  )
);

export default useBundleStore;

