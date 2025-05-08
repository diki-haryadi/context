/== AffiliateDashboardPage.tsx
import React, { useState, useEffect } from 'react';
import DashboardLayout from '../components/templates/DashboardLayout';
import AffiliateDashboard from '../components/organisms/AffiliateDashboard';
import Heading from '../components/atoms/Heading';
import Text from '../components/atoms/Text';
import Card from '../components/atoms/Card';
import Button from '../components/atoms/Button';
import { useNavigate } from 'react-router-dom';

// Simulated affiliate user data
const mockUserData = {
  name: "John Doe",
  referralCode: "JOHNDOE2024",
  totalEarnings: 2500000,
  availableEarnings: 1500000,
  pendingEarnings: 1000000,
  referralCount: 18,
  conversionCount: 12,
  conversionRate: 66.7,
  level: "silver" as const,
  progress: 12,
  nextLevelThreshold: 20,
};

// Simulated referrals
const mockReferrals = [
  {
    id: "1",
    name: "Sarah Johnson",
    date: "8 Mei 2025",
    status: "converted" as const,
    commission: 250000,
  },
  {
    id: "2",
    name: "David Williams",
    date: "5 Mei 2025",
    status: "converted" as const,
    commission: 250000,
  },
  {
    id: "3",
    name: "Michael Brown",
    date: "2 Mei 2025",
    status: "converted" as const,
    commission: 250000,
  },
  {
    id: "4",
    name: "Emily Davis",
    date: "30 April 2025",
    status: "pending" as const,
    commission: 250000,
  },
  {
    id: "5",
    name: "James Wilson",
    date: "28 April 2025",
    status: "converted" as const,
    commission: 250000,
  },
  {
    id: "6",
    name: "Jennifer Lee",
    date: "25 April 2025",
    status: "expired" as const,
    commission: 0,
  },
  {
    id: "7",
    name: "Robert Garcia",
    date: "20 April 2025",
    status: "converted" as const,
    commission: 250000,
  },
];

// Simulated payout history
const mockPayouts = [
  {
    id: "PAY-123456",
    amount: 750000,
    status: "completed" as const,
    date: "1 Mei 2025",
    method: "Bank Transfer",
  },
  {
    id: "PAY-123457",
    amount: 500000,
    status: "processed" as const,
    date: "1 April 2025",
    method: "Bank Transfer",
  },
  {
    id: "PAY-123458",
    amount: 250000,
    status: "completed" as const,
    date: "1 Maret 2025",
    method: "Bank Transfer",
  },
];

const AffiliateDashboardPage: React.FC = () => {
  const [isAffiliate, setIsAffiliate] = useState<boolean | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const navigate = useNavigate();

  // Simulating API call to check if user is an affiliate
  useEffect(() => {
    // In a real app, this would be an API call to check user's affiliate status
    const checkAffiliateStatus = () => {
      setTimeout(() => {
        // For demo purposes, we're setting to true
        // In a real app, this would be based on API response
        setIsAffiliate(true);
        setIsLoading(false);
      }, 1000);
    };

    checkAffiliateStatus();
  }, []);

  if (isLoading) {
    return (
      <DashboardLayout>
        <div className="flex justify-center items-center h-64">
          <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
        </div>
      </DashboardLayout>
    );
  }

  if (isAffiliate === false) {
    return (
      <DashboardLayout>
        <div className="max-w-4xl mx-auto py-8 px-4">
          <Card className="p-8 text-center">
            <div className="mb-6">
              <div className="inline-flex items-center justify-center w-16 h-16 rounded-full bg-blue-100 text-blue-600">
                <svg 
                  className="w-8 h-8" 
                  fill="none" 
                  stroke="currentColor" 
                  viewBox="0 0 24 24" 
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path 
                    strokeLinecap="round" 
                    strokeLinejoin="round" 
                    strokeWidth={2} 
                    d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                  />
                </svg>
              </div>
            </div>
            <Heading level={2} className="mb-4">Anda belum menjadi Afiliator</Heading>
            <Text className="text-gray-500 mb-6 max-w-lg mx-auto">
              Bergabunglah dengan program afiliasi kami dan dapatkan komisi untuk setiap orang yang 
              Anda referensikan ke kursus kami.
            </Text>
            <Button onClick={() => navigate('/affiliate/register')}>
              Daftar Program Afiliasi
            </Button>
          </Card>
        </div>
      </DashboardLayout>
    );
  }

  return (
    <DashboardLayout>
      <div className="max-w-7xl mx-auto py-8 px-4">
        <div className="mb-6">
          <Heading level={2}>Dashboard Afiliasi</Heading>
          <Text className="text-gray-500">
            Kelola program afiliasi dan pantau kinerja referral Anda
          </Text>
        </div>

        <AffiliateDashboard 
          userData={mockUserData}
          referrals={mockReferrals}
          payouts={mockPayouts}
        />
      </div>
    </DashboardLayout>
  );
};

export default AffiliateDashboardPage;

/== AffiliateRegistrationPage.tsx
import React, { useState } from "react";
import Heading from "../components/atoms/Heading";
import Text from "../components/atoms/Text";
import AffiliateApplicationForm from "../components/organisms/AffiliateApplicationForm";
import MainLayout from "../components/templates/MainLayout";
import Card from "../components/atoms/Card";
import Button from "../components/atoms/Button";

const AffiliateRegistrationPage: React.FC = () => {
  const [applicationSubmitted, setApplicationSubmitted] = useState(false);

  const handleApplicationSubmit = (data: any) => {
    // Here you would normally send the data to your backend
    console.log("Application submitted:", data);
    setApplicationSubmitted(true);
    window.scrollTo(0, 0);
  };

  return (
    <MainLayout>
      <div className="py-10 px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto">
        <div className="max-w-4xl mx-auto">
          {applicationSubmitted ? (
            <Card className="p-8 text-center">
              <div className="inline-flex items-center justify-center w-16 h-16 rounded-full bg-green-100 mb-6">
                <svg
                  className="w-8 h-8 text-green-600"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth={2}
                    d="M5 13l4 4L19 7"
                  />
                </svg>
              </div>
              <Heading level={2} className="mb-2">
                Aplikasi Anda Telah Diterima!
              </Heading>
              <Text className="text-gray-500 mb-6 max-w-lg mx-auto">
                Terima kasih telah mendaftar untuk program afiliasi kami. Kami akan meninjau
                aplikasi Anda dan menghubungi Anda melalui email dalam 2-3 hari kerja.
              </Text>
              <Button onClick={() => window.location.href = "/"}>
                Kembali ke Beranda
              </Button>
            </Card>
          ) : (
            <>
              <div className="text-center mb-10">
                <Heading level={1} className="mb-4">
                  Program Afiliasi
                </Heading>
                <Text className="text-xl text-gray-500 max-w-3xl mx-auto">
                  Dapatkan komisi untuk setiap orang yang Anda referensikan ke kursus kami. 
                  Bergabunglah dengan program afiliasi kami hari ini!
                </Text>
              </div>

              <div className="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-12">
                <Card className="p-6 text-center">
                  <div className="inline-flex items-center justify-center w-12 h-12 rounded-full bg-blue-100 text-blue-600 mb-4">
                    <svg
                      className="w-6 h-6"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        strokeLinecap="round"
                        strokeLinejoin="round"
                        strokeWidth={2}
                        d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                      />
                    </svg>
                  </div>
                  <Heading level={3} className="mb-2">
                    Komisi Menarik
                  </Heading>
                  <Text className="text-gray-500">
                    Dapatkan komisi hingga 20% untuk setiap orang yang mendaftar melalui 
                    link referral Anda.
                  </Text>
                </Card>

                <Card className="p-6 text-center">
                  <div className="inline-flex items-center justify-center w-12 h-12 rounded-full bg-purple-100 text-purple-600 mb-4">
                    <svg
                      className="w-6 h-6"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        strokeLinecap="round"
                        strokeLinejoin="round"
                        strokeWidth={2}
                        d="M11 3.055A9.001 9.001 0 1020.945 13H11V3.055z"
                      />
                      <path
                        strokeLinecap="round"
                        strokeLinejoin="round"
                        strokeWidth={2}
                        d="M20.488 9H15V3.512A9.025 9.025 0 0120.488 9z"
                      />
                    </svg>
                  </div>
                  <Heading level={3} className="mb-2">
                    Pelacakan Real-time
                  </Heading>
                  <Text className="text-gray-500">
                    Pantau semua referral dan komisi Anda secara real-time
                    melalui dashboard afiliasi.
                  </Text>
                </Card>

                <Card className="p-6 text-center">
                  <div className="inline-flex items-center justify-center w-12 h-12 rounded-full bg-green-100 text-green-600 mb-4">
                    <svg
                      className="w-6 h-6"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        strokeLinecap="round"
                        strokeLinejoin="round"
                        strokeWidth={2}
                        d="M12 6v6m0 0v6m0-6h6m-6 0H6"
                      />
                    </svg>
                  </div>
                  <Heading level={3} className="mb-2">
                    Materi Promosi
                  </Heading>
                  <Text className="text-gray-500">
                    Akses berbagai materi promosi seperti banner, email template,
                    dan konten untuk sosial media.
                  </Text>
                </Card>
              </div>

              <div className="mb-12">
                <Card className="p-6">
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div>
                      <Heading level={3} className="mb-4">
                        Bagaimana Cara Kerjanya
                      </Heading>
                      <div className="space-y-4">
                        <div className="flex items-start">
                          <div className="flex-shrink-0 inline-flex items-center justify-center w-8 h-8 rounded-full bg-blue-100 text-blue-600 mr-3">
                            1
                          </div>
                          <div>
                            <Heading level={5} className="mb-1">
                              Daftar sebagai Afiliator
                            </Heading>
                            <Text className="text-gray-500">
                              Isi formulir pendaftaran afiliasi dan tunggu persetujuan dari tim kami.
                            </Text>
                          </div>
                        </div>
                        <div className="flex items-start">
                          <div className="flex-shrink-0 inline-flex items-center justify-center w-8 h-8 rounded-full bg-blue-100 text-blue-600 mr-3">
                            2
                          </div>
                          <div>
                            <Heading level={5} className="mb-1">
                              Dapatkan Link Referral
                            </Heading>
                            <Text className="text-gray-500">
                              Setelah disetujui, Anda akan mendapatkan link referral unik dan akses ke dashboard afiliasi.
                            </Text>
                          </div>
                        </div>
                        <div className="flex items-start">
                          <div className="flex-shrink-0 inline-flex items-center justify-center w-8 h-8 rounded-full bg-blue-100 text-blue-600 mr-3">
                            3
                          </div>
                          <div>
                            <Heading level={5} className="mb-1">
                              Promosikan Kursus Kami
                            </Heading>
                            <Text className="text-gray-500">
                              Bagikan link referral Anda melalui sosial media, blog, email, atau metode promosi lainnya.
                            </Text>
                          </div>
                        </div>
                        <div className="flex items-start">
                          <div className="flex-shrink-0 inline-flex items-center justify-center w-8 h-8 rounded-full bg-blue-100 text-blue-600 mr-3">
                            4
                          </div>
                          <div>
                            <Heading level={5} className="mb-1">
                              Terima Komisi
                            </Heading>
                            <Text className="text-gray-500">
                              Dapatkan komisi untuk setiap pendaftaran yang berhasil melalui link referral Anda.
                            </Text>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div>
                      <Heading level={3} className="mb-4">
                        Keuntungan Program Afiliasi
                      </Heading>
                      <ul className="space-y-3">
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Komisi hingga 20% untuk setiap pendaftaran</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Cookie tracking selama 30 hari</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Dashboard pelacakan real-time</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Pembayaran komisi setiap bulan</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Berbagai pilihan metode pembayaran</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Akses ke materi promosi profesional</Text>
                        </li>
                        <li className="flex items-start">
                          <svg
                            className="w-5 h-5 text-green-500 mr-2 mt-0.5"
                            fill="none"
                            stroke="currentColor"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                          >
                            <path
                              strokeLinecap="round"
                              strokeLinejoin="round"
                              strokeWidth={2}
                              d="M5 13l4 4L19 7"
                            />
                          </svg>
                          <Text>Program reward tambahan untuk afiliator terbaik</Text>
                        </li>
                      </ul>
                    </div>
                  </div>
                </Card>
              </div>

              <AffiliateApplicationForm onSubmit={handleApplicationSubmit} />
            </>
          )}
        </div>
      </div>
    </MainLayout>
  );
};

export default AffiliateRegistrationPage;

/== BillingPage.tsx
import React from 'react';
import { Download, CreditCard, Calendar } from 'lucide-react';

const BillingPage = () => {
  const transactions = [
    {
      id: 'INV-2024-001',
      date: '2024-03-25',
      amount: 578317,
      status: 'paid',
      description: 'Premium Plan - Annual Subscription'
    },
    {
      id: 'INV-2024-002', 
      date: '2024-02-25',
      amount: 48194,
      status: 'paid',
      description: 'Premium Plan - Monthly Subscription'
    }
  ];

  return (
    <div className="min-h-screen bg-gray-50">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div className="mb-8">
          <h1 className="text-2xl font-bold text-gray-900">Billing & Payments</h1>
          <p className="text-gray-600">Manage your subscription and view payment history</p>
        </div>

        {/* Current Plan */}
        <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-6 mb-8">
          <div className="flex items-center justify-between">
            <div>
              <h2 className="text-lg font-medium text-gray-900 mb-1">Current Plan</h2>
              <p className="text-gray-600">Premium Plan - Annual Subscription</p>
            </div>
            <div className="flex items-center">
              <span className="inline-flex items-center px-3 py-1 rounded-full text-sm font-medium bg-green-100 text-green-800 mr-4">
                Active
              </span>
              <button className="text-primary-600 hover:text-primary-700 font-medium">
                Change Plan
              </button>
            </div>
          </div>

          <div className="mt-6 pt-6 border-t border-gray-200">
            <div className="flex items-center justify-between text-sm">
              <div className="text-gray-500">Next billing date</div>
              <div className="font-medium text-gray-900">March 25, 2025</div>
            </div>
          </div>
        </div>

        {/* Payment Method */}
        <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-6 mb-8">
          <h2 className="text-lg font-medium text-gray-900 mb-4">Payment Method</h2>
          
          <div className="flex items-center">
            <div className="h-8 w-12 bg-gray-100 rounded flex items-center justify-center mr-3">
              <CreditCard className="h-4 w-4 text-gray-600" />
            </div>
            <div>
              <div className="font-medium text-gray-900">Visa ending in 4242</div>
              <div className="text-sm text-gray-500">Expires 12/24</div>
            </div>
            <button className="ml-auto text-primary-600 hover:text-primary-700 font-medium">
              Update
            </button>
          </div>
        </div>

        {/* Billing History */}
        <div className="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
          <div className="p-6 border-b border-gray-200">
            <h2 className="text-lg font-medium text-gray-900">Billing History</h2>
          </div>
          
          <div className="divide-y divide-gray-200">
            {transactions.map((transaction) => (
              <div key={transaction.id} className="p-6 flex items-center justify-between">
                <div className="flex items-center">
                  <div className="h-10 w-10 bg-gray-100 rounded-full flex items-center justify-center mr-4">
                    <Calendar className="h-5 w-5 text-gray-600" />
                  </div>
                  <div>
                    <div className="font-medium text-gray-900">{transaction.description}</div>
                    <div className="text-sm text-gray-500">
                      {new Date(transaction.date).toLocaleDateString('en-US', {
                        year: 'numeric',
                        month: 'long',
                        day: 'numeric'
                      })}
                    </div>
                  </div>
                </div>
                
                <div className="flex items-center">
                  <div className="mr-8">
                    <div className="font-medium text-gray-900">
                      Rp {transaction.amount.toLocaleString()}
                    </div>
                    <div className="text-sm">
                      <span className="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-green-100 text-green-800">
                        {transaction.status}
                      </span>
                    </div>
                  </div>
                  <button className="text-gray-400 hover:text-gray-500">
                    <Download className="h-5 w-5" />
                  </button>
                </div>
              </div>
            ))}
          </div>
        </div>
      </div>
    </div>
  );
};

export default BillingPage;

/== CertificatePage.tsx
import React from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Download, Share, ArrowLeft } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';
import { useAuthStore } from '../store/authStore';
import Header from '../components/organisms/Header';

const CertificatePage: React.FC = () => {
  const { courseId } = useParams<{ courseId: string }>();
  const navigate = useNavigate();
  const { getCourseById } = useCourseStore();
  const { user } = useAuthStore();
  const course = getCourseById(courseId || '');

  if (!course || !user) {
    return null;
  }

  const certificateDate = new Date().toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 to-gray-800 py-8">
      <Header />
      {/* Header Actions */}
      <div className="max-w-5xl mx-auto px-4 mb-8 mt-8">
        <div className="flex justify-between items-center">
          <button
            onClick={() => navigate(-1)}
            className="flex items-center px-4 py-2 text-gray-300 hover:text-white transition-colors"
          >
            <ArrowLeft className="h-5 w-5 mr-2" />
            Back
          </button>
          <div className="flex space-x-4">
            <button className="flex items-center px-4 py-2 bg-white/10 text-white rounded-lg hover:bg-white/20 transition-colors">
              <Share className="h-5 w-5 mr-2" />
              Share
            </button>
            <button className="flex items-center px-4 py-2 bg-primary-500 text-white rounded-lg hover:bg-primary-600 transition-colors">
              <Download className="h-5 w-5 mr-2" />
              Download
            </button>
          </div>
        </div>
      </div>

      {/* Certificate Card */}
      <div className="max-w-5xl mx-auto px-4">
        <div className="bg-[#1a1f2e] rounded-xl p-16 relative overflow-hidden">
          {/* Background Pattern */}
          <div className="absolute inset-0 bg-grid-pattern opacity-5"></div>
          
          {/* Certificate Content */}
          <div className="relative">
            {/* Header */}
            <div className="text-center mb-16">
              <div className="inline-flex items-center justify-center w-16 h-16 bg-primary-500 rounded-full mb-6">
                <img src="/logo.svg" alt="Logo" className="w-8 h-8" />
              </div>
              <h1 className="text-4xl font-serif text-white mb-4">Certificate of Completion</h1>
              <p className="text-gray-400">This is to certify that</p>
            </div>

            {/* Student Name */}
            <div className="text-center mb-16">
              <h2 className="text-4xl font-bold text-primary-400 mb-2">{user.name}</h2>
              <p className="text-gray-400">has successfully completed the course</p>
            </div>

            {/* Course Title */}
            <div className="text-center mb-16">
              <h3 className="text-3xl font-bold text-white mb-4">{course.title}</h3>
              <div className="flex items-center justify-center">
                <div className="h-px w-16 bg-gray-700"></div>
                <span className="mx-4 text-gray-400">{certificateDate}</span>
                <div className="h-px w-16 bg-gray-700"></div>
              </div>
            </div>

            {/* Footer */}
            <div className="flex justify-between items-end">
              <div className="text-center">
                <div className="mb-8">
                  <img src="/signature.png" alt="Signature" className="h-12 mx-auto mb-2" />
                  <div className="h-px w-48 bg-gray-700 mb-2"></div>
                  <p className="text-lg font-serif text-white">John Smith</p>
                  <p className="text-sm text-gray-400">Course Instructor</p>
                </div>
              </div>

              {/* Certificate Seal */}
              <div className="flex flex-col items-center">
                <div className="w-24 h-24 border-2 border-primary-500 rounded-full flex items-center justify-center mb-4">
                  <div className="text-primary-400 font-serif text-sm text-center">
                    Coder
                    <br />
                    Growth
                  </div>
                </div>
                <p className="text-sm text-gray-400">Certificate ID: {courseId}-{user.id}</p>
              </div>
            </div>
          </div>
        </div>

        {/* Certificate Details */}
        <div className="mt-8 bg-white/5 backdrop-blur-sm rounded-xl p-8">
          <h2 className="text-xl font-bold text-white mb-8">Certificate Details</h2>
          
          <div className="grid grid-cols-2 gap-8 mb-8">
            <div>
              <p className="text-sm text-gray-400 mb-1">Course</p>
              <p className="text-lg text-white">{course.title}</p>
            </div>
            <div>
              <p className="text-sm text-gray-400 mb-1">Completion Date</p>
              <p className="text-lg text-white">{certificateDate}</p>
            </div>
            <div>
              <p className="text-sm text-gray-400 mb-1">Duration</p>
              <p className="text-lg text-white">{course.duration}</p>
            </div>
            <div>
              <p className="text-sm text-gray-400 mb-1">Certificate ID</p>
              <p className="text-lg text-white">{courseId}-{user.id}</p>
            </div>
          </div>

          <div className="border-t border-gray-700 pt-8">
            <h3 className="text-lg font-medium text-white mb-4">Skills Verified</h3>
            <div className="flex flex-wrap gap-2">
              {course.features.map((feature, index) => (
                <span
                  key={index}
                  className="px-3 py-1 bg-primary-500/20 text-primary-300 rounded-full text-sm border border-primary-500/30"
                >
                  {feature}
                </span>
              ))}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default CertificatePage;

/== CertificatesDashboardPage.tsx
import React from 'react';
import { useNavigate } from 'react-router-dom';
import { Award, ChevronRight, Download, Search, Calendar, Shield } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';
import Card from '../components/atoms/Card';
import SearchInput from '../components/molecules/SearchInput';
import Header from '../components/organisms/Header';

const CertificatesDashboardPage: React.FC = () => {
  const navigate = useNavigate();
  const { enrolledCourses } = useCourseStore();
  const [searchQuery, setSearchQuery] = React.useState('');
  const [selectedCategory, setSelectedCategory] = React.useState('all');

  // Group courses by category
  const coursesByCategory = enrolledCourses.reduce((acc, course) => {
    const category = course.category || 'Other';
    if (!acc[category]) {
      acc[category] = [];
    }
    acc[category].push(course);
    return acc;
  }, {} as Record<string, typeof enrolledCourses>);

  // Filter courses based on search query and category
  const filteredCourses = enrolledCourses.filter(course => {
    const matchesSearch = course.title.toLowerCase().includes(searchQuery.toLowerCase());
    const matchesCategory = selectedCategory === 'all' || course.category === selectedCategory;
    return matchesSearch && matchesCategory;
  });

  const handleCertificateClick = (courseId: string) => {
    navigate(`/certificate/${courseId}`);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 to-gray-800">
    <Header />
      
      {/* Header */}
      <div className="relative overflow-hidden">
        <div className="absolute inset-0 bg-grid-pattern opacity-10"></div>
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16 relative">
          <div className="md:flex md:items-center md:justify-between">
            <div className="flex-1 min-w-0">
              <div className="flex items-center mb-4">
                <div className="bg-primary-500 p-3 rounded-xl mr-4">
                  <Award className="h-8 w-8 text-white" />
                </div>
                <div>
                  <h1 className="text-4xl font-bold text-white mb-2">
                    My Certificates
                  </h1>
                  <p className="text-xl text-gray-300">
                    View and manage your earned certificates
                  </p>
                </div>
              </div>

              <div className="mt-8 flex flex-wrap gap-4">
                <div className="bg-white/10 backdrop-blur-lg rounded-lg px-4 py-3 flex items-center">
                  <Award className="h-5 w-5 text-primary-400 mr-2" />
                  <div>
                    <div className="text-2xl font-bold text-white">{filteredCourses.length}</div>
                    <div className="text-sm text-gray-400">Total Certificates</div>
                  </div>
                </div>
                <div className="bg-white/10 backdrop-blur-lg rounded-lg px-4 py-3 flex items-center">
                  <Calendar className="h-5 w-5 text-green-400 mr-2" />
                  <div>
                    <div className="text-2xl font-bold text-white">May 2025</div>
                    <div className="text-sm text-gray-400">Latest Achievement</div>
                  </div>
                </div>
                <div className="bg-white/10 backdrop-blur-lg rounded-lg px-4 py-3 flex items-center">
                  <Shield className="h-5 w-5 text-yellow-400 mr-2" />
                  <div>
                    <div className="text-2xl font-bold text-white">Verified</div>
                    <div className="text-sm text-gray-400">Certificate Status</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* Content */}
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 -mt-8">
        {/* Search and Filters */}
        <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 mb-8">
          <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
            <SearchInput
              value={searchQuery}
              onChange={setSearchQuery}
              placeholder="Search certificates..."
              className="max-w-md"
            />
            <div className="flex items-center space-x-4">
              <select
                value={selectedCategory}
                onChange={(e) => setSelectedCategory(e.target.value)}
                className="bg-white/10 text-white border border-gray-600 rounded-lg px-4 py-2 focus:outline-none focus:ring-2 focus:ring-primary-500"
              >
                <option value="all">All Categories</option>
                {Object.keys(coursesByCategory).map(category => (
                  <option key={category} value={category}>{category}</option>
                ))}
              </select>
            </div>
          </div>
        </div>

        {/* Certificates Grid */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {filteredCourses.map((course) => (
            <Card
              key={course.id}
              className="group hover:scale-105 transition-all duration-300 cursor-pointer bg-white/10 backdrop-blur-lg border border-white/10"
              onClick={() => handleCertificateClick(course.id)}
            >
              <div className="p-6">
                <div className="flex items-start justify-between">
                  <div className={`${course.color} p-3 rounded-xl`}>
                    <Award className="h-6 w-6 text-white" />
                  </div>
                  <button className="p-2 hover:bg-white/10 rounded-lg transition-colors">
                    <Download className="h-5 w-5 text-gray-400" />
                  </button>
                </div>

                <div className="mt-6">
                  <h3 className="text-xl font-semibold text-white mb-2">
                    {course.title}
                  </h3>
                  <div className="flex items-center text-sm text-gray-400 mb-4">
                    <Calendar className="h-4 w-4 mr-2" />
                    <span>Completed on May 15, 2025</span>
                  </div>
                  <div className="flex flex-wrap gap-2 mb-6">
                    {(course.features || []).slice(0, 3).map((feature, index) => (
                      <span
                        key={index}
                        className="px-2.5 py-1 rounded-full text-xs font-medium bg-primary-500/20 text-primary-300 border border-primary-500/30"
                      >
                        {feature}
                      </span>
                    ))}
                  </div>
                </div>

                <div className="pt-4 border-t border-gray-700">
                  <div className="flex items-center justify-between text-sm">
                    <div className="text-gray-400">
                      ID: {course.id}
                    </div>
                    <div className="flex items-center text-primary-400 font-medium">
                      View Certificate
                      <ChevronRight className="h-4 w-4 ml-1 group-hover:translate-x-1 transition-transform" />
                    </div>
                  </div>
                </div>
              </div>
            </Card>
          ))}
        </div>

        {/* Empty State */}
        {filteredCourses.length === 0 && (
          <div className="text-center py-16 bg-white/5 rounded-xl backdrop-blur-lg">
            <Award className="mx-auto h-16 w-16 text-gray-600 mb-4" />
            <h3 className="text-xl font-medium text-white mb-2">
              No certificates found
            </h3>
            <p className="text-gray-400">
              {searchQuery
                ? "No certificates match your search criteria"
                : "Complete courses to earn certificates"}
            </p>
          </div>
        )}
      </div>
    </div>
  );
};

export default CertificatesDashboardPage;

/== CompilerPage.tsx
import React, { useState } from 'react';
import { Play, Download, Copy, Trash, Settings, ChevronDown } from 'lucide-react';
import Card from '../components/atoms/Card';
// ... other imports

const CompilerPage = () => {
  // ... component code stays the same, just remove MainLayout wrapper
  return (
    <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      {/* ... rest of the JSX */}
    </div>
  );
};

export default CompilerPage;

/== CompleteLearningPage.tsx
import React from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Trophy, Download, Share, ArrowRight, Star, Clock, Users, CheckCircle } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';
import Button from '../components/atoms/Button';
import Card from '../components/atoms/Card';
import Confetti from '../components/atoms/Confetti';
import UserAvatar from '../components/molecules/UserAvatar';
import ProgressBar from '../components/molecules/ProgressBar';
import Badge from '../components/atoms/Badge';
import Header from '../components/organisms/Header';

const CompleteLearningPage: React.FC = () => {
  const { courseId } = useParams<{ courseId: string }>();
  const navigate = useNavigate();
  const { getCourseById } = useCourseStore();
  const course = getCourseById(courseId || '');

  if (!course) {
    return null;
  }

  const handleViewCertificate = () => {
    navigate(`/certificate/${courseId}`);
  };

  const handleShareSuccess = () => {
    // Implement share functionality
  };

  const handleDownloadCertificate = () => {
    // Implement certificate download
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-600 to-purple-700">
      <Header />
      <Confetti />
      
      <div className="max-w-4xl mx-auto px-4 py-16">
        {/* Completion Card */}
        <Card className="text-center p-8 mb-8 bg-white/10 backdrop-blur-lg border border-white/20">
          <div className="mb-8">
            <div className="w-24 h-24 bg-yellow-500 rounded-full flex items-center justify-center mx-auto mb-6">
              <Trophy className="h-12 w-12 text-white" />
            </div>
            <h1 className="text-4xl font-bold text-white mb-4">
              Congratulations! 🎉
            </h1>
            <p className="text-xl text-white/90">
              You've successfully completed the course
            </p>
          </div>

          <div className="text-3xl font-bold text-white mb-8">
            {course.title}
          </div>

          <div className="grid grid-cols-3 gap-6 mb-8">
            <div className="bg-white/5 rounded-lg p-4">
              <div className="text-2xl font-bold text-white">100%</div>
              <div className="text-sm text-white/70">Completion Rate</div>
            </div>
            <div className="bg-white/5 rounded-lg p-4">
              <div className="text-2xl font-bold text-white">500</div>
              <div className="text-sm text-white/70">XP Earned</div>
            </div>
            <div className="bg-white/5 rounded-lg p-4">
              <div className="text-2xl font-bold text-white">12</div>
              <div className="text-sm text-white/70">Hours Spent</div>
            </div>
          </div>

          <div className="flex justify-center space-x-4">
            <Button
              variant="primary"
              onClick={handleViewCertificate}
              className="text-blue-600 hover:bg-gray-100"
            >
              View Certificate
            </Button>
            <Button
              variant="outline"
              onClick={handleShareSuccess}
              className="border-white text-white hover:bg-white/10"
            >
              Share Achievement
            </Button>
          </div>
        </Card>

        {/* Course Details */}
        <Card className="p-8 bg-white dark:bg-gray-800">
          <div className="flex items-center justify-between mb-8">
            <div>
              <h2 className="text-2xl font-bold text-gray-900 dark:text-white mb-2">
                Course Overview
              </h2>
              <p className="text-gray-600 dark:text-gray-300">
                Here's what you've accomplished
              </p>
            </div>
            <div className="flex items-center">
              <Star className="h-5 w-5 text-yellow-400 fill-current" />
              <span className="ml-2 text-gray-900 dark:text-white font-medium">
                {course.rating}
              </span>
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-8 mb-8">
            <div>
              <h3 className="font-medium text-gray-900 dark:text-white mb-4">
                Course Statistics
              </h3>
              <div className="space-y-4">
                <div className="flex items-center text-gray-600 dark:text-gray-300">
                  <Clock className="h-5 w-5 mr-3" />
                  <span>{course.duration}</span>
                </div>
                <div className="flex items-center text-gray-600 dark:text-gray-300">
                  <Users className="h-5 w-5 mr-3" />
                  <span>{course.students.toLocaleString()} students</span>
                </div>
                <div className="flex items-center text-gray-600 dark:text-gray-300">
                  <CheckCircle className="h-5 w-5 mr-3" />
                  <span>{course.level}</span>
                </div>
              </div>
            </div>

            <div>
              <h3 className="font-medium text-gray-900 dark:text-white mb-4">
                Your Progress
              </h3>
              <div className="space-y-4">
                <div>
                  <div className="flex justify-between mb-1">
                    <span className="text-sm text-gray-600 dark:text-gray-300">
                      Course Completion
                    </span>
                    <span className="text-sm font-medium text-gray-900 dark:text-white">
                      100%
                    </span>
                  </div>
                  <ProgressBar value={100} color="success" />
                </div>
                <div>
                  <div className="flex justify-between mb-1">
                    <span className="text-sm text-gray-600 dark:text-gray-300">
                      Quizzes Completed
                    </span>
                    <span className="text-sm font-medium text-gray-900 dark:text-white">
                      12/12
                    </span>
                  </div>
                  <ProgressBar value={100} color="success" />
                </div>
                <div>
                  <div className="flex justify-between mb-1">
                    <span className="text-sm text-gray-600 dark:text-gray-300">
                      Projects Completed
                    </span>
                    <span className="text-sm font-medium text-gray-900 dark:text-white">
                      3/3
                    </span>
                  </div>
                  <ProgressBar value={100} color="success" />
                </div>
              </div>
            </div>
          </div>

          <div className="border-t border-gray-200 dark:border-gray-700 pt-8">
            <h3 className="font-medium text-gray-900 dark:text-white mb-4">
              Skills Acquired
            </h3>
            <div className="flex flex-wrap gap-2">
              {course.features.map((feature, index) => (
                <Badge key={index} variant="primary">
                  {feature}
                </Badge>
              ))}
            </div>
          </div>
        </Card>
      </div>
    </div>
  );
};

export default CompleteLearningPage;

/== CourseEnrollmentPage.tsx
import React, { useState } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Play, ArrowRight, Share, CheckCircle, Star, Clock, Users, BookOpen, Award, ChevronDown, ChevronRight } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';
import { useAuthStore } from '../store/authStore';

const CourseEnrollmentPage: React.FC = () => {
  const { courseId } = useParams<{ courseId: string }>();
  const navigate = useNavigate();
  const { enrolledCourses, enrollInCourse, getCourseById } = useCourseStore();
  const { isAuthenticated } = useAuthStore();
  const [showEnrollSuccess, setShowEnrollSuccess] = useState(false);
  const [expandedChapter, setExpandedChapter] = useState<string | null>('chapter-1');

  const course = getCourseById(courseId || '');
  const isEnrolled = enrolledCourses.some(c => c.id === courseId);

  if (!course) {
    return (
      <div className="min-h-screen bg-gray-50 dark:bg-gray-900 flex items-center justify-center p-4">
        <div className="bg-white dark:bg-gray-800 rounded-xl p-8 text-center max-w-lg">
          <h1 className="text-3xl font-bold text-gray-900 dark:text-white mb-4">Course Not Found</h1>
          <p className="text-gray-600 dark:text-gray-300 mb-6">
            The course you're looking for doesn't exist or has been moved.
          </p>
          <button
            onClick={() => navigate('/courses')}
            className="inline-flex items-center px-6 py-3 bg-primary-500 text-white rounded-lg hover:bg-primary-600 transition duration-200"
          >
            Browse Courses
          </button>
        </div>
      </div>
    );
  }

  const handleEnrollment = () => {
    if (!isAuthenticated) {
      // Show login modal or redirect to login
      return;
    }

    if (isEnrolled) {
      navigate(`/courses/${courseId}/lessons/${course.firstLessonId}`);
    } else {
      enrollInCourse(courseId || '');
      setShowEnrollSuccess(true);
      setTimeout(() => {
        navigate(`/courses/${courseId}/lessons/${course.firstLessonId}`);
      }, 2000);
    }
  };

  const curriculum = [
    {
      id: 'chapter-1',
      title: 'Introduction to Go',
      duration: '45 minutes',
      lessons: [
        { id: 'lesson-1', title: 'What is Go?', duration: '10 min', isCompleted: false },
        { id: 'lesson-2', title: 'Setting up Go Environment', duration: '15 min', isCompleted: false },
        { id: 'lesson-3', title: 'Your First Go Program', duration: '20 min', isCompleted: false },
      ]
    },
    {
      id: 'chapter-2',
      title: 'Basic Syntax and Data Types',
      duration: '1 hour',
      lessons: [
        { id: 'lesson-4', title: 'Variables and Constants', duration: '15 min', isCompleted: false },
        { id: 'lesson-5', title: 'Basic Data Types', duration: '25 min', isCompleted: false },
        { id: 'lesson-6', title: 'Type Conversion', duration: '20 min', isCompleted: false },
      ]
    },
    {
      id: 'chapter-3',
      title: 'Control Structures',
      duration: '1.5 hours',
      lessons: [
        { id: 'lesson-7', title: 'If Statements', duration: '30 min', isCompleted: false },
        { id: 'lesson-8', title: 'Loops in Go', duration: '30 min', isCompleted: false },
        { id: 'lesson-9', title: 'Switch Statements', duration: '30 min', isCompleted: false },
      ]
    }
  ];

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      {/* Success Alert */}
      {showEnrollSuccess && (
        <div className="fixed top-4 right-4 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg z-50 animate-fade-in-down">
          <div className="flex items-center">
            <CheckCircle className="h-5 w-5 mr-2" />
            <span>Successfully enrolled! Redirecting to course...</span>
          </div>
        </div>
      )}

      {/* Hero Section */}
      <div className="relative overflow-hidden bg-gradient-to-br from-cyan-500 to-blue-600 dark:from-cyan-900 dark:to-blue-900">
        <div className="absolute inset-0 bg-grid-pattern opacity-10"></div>
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16 relative">
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-12 items-center">
            <div>
              {/* Breadcrumbs */}
              <nav className="flex mb-6" aria-label="Breadcrumb">
                <ol className="flex items-center space-x-2 text-white/80">
                  <li><a href="/" className="hover:text-white">Home</a></li>
                  <ChevronRight className="h-4 w-4" />
                  <li><a href="/courses" className="hover:text-white">Courses</a></li>
                  <ChevronRight className="h-4 w-4" />
                  <li className="text-white font-medium">{course.title}</li>
                </ol>
              </nav>

              <h1 className="text-4xl font-bold text-white mb-4">{course.title}</h1>
              <p className="text-xl text-white/90 mb-6">{course.description}</p>

              <div className="flex flex-wrap gap-6 mb-8">
                <div className="flex items-center text-white">
                  <Star className="h-5 w-5 text-yellow-400 fill-current mr-2" />
                  <span>{course.rating}</span>
                  <span className="mx-2">•</span>
                  <span>{course.students.toLocaleString()} students</span>
                </div>
                <div className="flex items-center text-white">
                  <Clock className="h-5 w-5 mr-2" />
                  <span>{course.duration}</span>
                </div>
                <div className="flex items-center text-white">
                  <BookOpen className="h-5 w-5 mr-2" />
                  <span>{course.level}</span>
                </div>
              </div>

              <div className="flex items-center mb-8">
                <img
                  src="https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80"
                  alt="Instructor"
                  className="w-12 h-12 rounded-full border-2 border-white"
                />
                <div className="ml-4">
                  <div className="text-white font-medium">John Smith</div>
                  <div className="text-white/80 text-sm">Senior Software Engineer</div>
                </div>
              </div>

              <button
                onClick={handleEnrollment}
                className="inline-flex items-center px-8 py-4 bg-white text-blue-600 rounded-lg hover:bg-gray-50 transition duration-200 font-medium text-lg shadow-lg"
              >
                {isEnrolled ? (
                  <>
                    <Play className="h-5 w-5 mr-2" />
                    Continue Learning
                  </>
                ) : (
                  <>
                    <BookOpen className="h-5 w-5 mr-2" />
                    Start Learning Now
                  </>
                )}
              </button>
            </div>

            <div className="hidden lg:block">
              <div className="relative">
                <div className="absolute -inset-4 bg-white/20 rounded-2xl blur-xl"></div>
                <img
                  src={course.image}
                  alt={course.title}
                  className="relative rounded-2xl shadow-2xl"
                />
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* Course Content */}
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
        <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
          {/* Main Content */}
          <div className="lg:col-span-2">
            {/* What You'll Learn */}
            <div className="bg-white dark:bg-gray-800 rounded-xl p-8 mb-8 shadow-sm">
              <h2 className="text-2xl font-bold text-gray-900 dark:text-white mb-6">
                What You'll Learn
              </h2>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {course.features.map((feature, index) => (
                  <div key={index} className="flex items-start">
                    <CheckCircle className="h-5 w-5 text-green-500 mr-3 flex-shrink-0 mt-0.5" />
                    <span className="text-gray-700 dark:text-gray-300">{feature}</span>
                  </div>
                ))}
              </div>
            </div>

            {/* Course Curriculum */}
            <div className="bg-gray-800 rounded-xl shadow-sm overflow-hidden">
              <div className="p-6 border-b border-gray-700">
                <h2 className="text-2xl font-bold text-white">
                  Course Curriculum
                </h2>
              </div>
              
              <div className="divide-y divide-gray-700">
                {curriculum.map((chapter) => (
                  <div key={chapter.id} className="bg-gray-800">
                    <button
                      onClick={() => setExpandedChapter(
                        expandedChapter === chapter.id ? null : chapter.id
                      )}
                      className="flex items-center justify-between w-full p-6 hover:bg-gray-750 transition-colors duration-200"
                    >
                      <div className="flex items-center">
                        <div className="mr-4">
                          {expandedChapter === chapter.id ? (
                            <ChevronDown className="h-5 w-5 text-gray-400" />
                          ) : (
                            <ChevronRight className="h-5 w-5 text-gray-400" />
                          )}
                        </div>
                        <div className="text-left">
                          <h3 className="text-lg font-medium text-white">
                            {chapter.title}
                          </h3>
                          <p className="text-sm text-gray-400">
                            {chapter.duration}
                          </p>
                        </div>
                      </div>
                    </button>
                    
                    {expandedChapter === chapter.id && (
                      <div className="bg-gray-750 px-6 pb-6">
                        <div className="space-y-4">
                          {chapter.lessons.map((lesson) => (
                            <div
                              key={lesson.id}
                              className="flex items-center justify-between p-4 bg-gray-700 rounded-lg hover:bg-gray-600 transition-colors duration-200 cursor-pointer"
                            >
                              <div className="flex items-center">
                                <div className="w-8 h-8 bg-blue-900 rounded-full flex items-center justify-center mr-4">
                                  <Play className="h-4 w-4 text-blue-400" />
                                </div>
                                <div>
                                  <h4 className="text-white font-medium">
                                    {lesson.title}
                                  </h4>
                                  <p className="text-sm text-gray-400">
                                    {lesson.duration}
                                  </p>
                                </div>
                              </div>
                              {lesson.isCompleted && (
                                <CheckCircle className="h-5 w-5 text-green-500" />
                              )}
                            </div>
                          ))}
                        </div>
                      </div>
                    )}
                  </div>
                ))}
              </div>
            </div>
          </div>

          {/* Sidebar */}
          <div className="lg:col-span-1">
            <div className="sticky top-8">
              <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm overflow-hidden">
                <div className="p-6">
                  <div className="space-y-4">
                    <div>
                      <div className="text-sm text-gray-500 dark:text-gray-400">Price</div>
                      <div className="text-2xl font-bold text-gray-900 dark:text-white">
                        {course.price}
                      </div>
                    </div>
                    
                    <button
                      onClick={handleEnrollment}
                      className="w-full inline-flex items-center justify-center px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition duration-200 font-medium"
                    >
                      {isEnrolled ? (
                        <>
                          <Play className="h-5 w-5 mr-2" />
                          Continue Learning
                        </>
                      ) : (
                        <>
                          <BookOpen className="h-5 w-5 mr-2" />
                          Enroll Now
                        </>
                      )}
                    </button>

                    <button
                      onClick={() => {/* Add share functionality */}}
                      className="w-full inline-flex items-center justify-center px-6 py-3 border border-gray-600 text-gray-300 rounded-lg hover:bg-gray-750 transition duration-200 font-medium"
                    >
                      <Share className="h-5 w-5 mr-2" />
                      Share Course
                    </button>

                    <div className="border-t border-gray-700 pt-4 mt-6">
                      <h3 className="text-lg font-medium text-white mb-4">
                        This course includes:
                      </h3>
                      <div className="space-y-3">
                        <div className="flex items-center text-gray-300">
                          <Clock className="h-5 w-5 mr-3" />
                          <span>{course.duration} of content</span>
                        </div>
                        <div className="flex items-center text-gray-300">
                          <BookOpen className="h-5 w-5 mr-3" />
                          <span>24/7 lifetime access</span>
                        </div>
                        <div className="flex items-center text-gray-300">
                          <Award className="h-5 w-5 mr-3" />
                          <span>Certificate of completion</span>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default CourseEnrollmentPage;

/== CoursePage.tsx
import React, { useState } from 'react';
import { useCourseStore } from '../store/courseStore';
import { Search, Filter, Clock, Users, Star, ChevronDown } from 'lucide-react';

const CoursePage = () => {
  const { listCourses } = useCourseStore();
  const [searchQuery, setSearchQuery] = useState('');
  const [selectedLevel, setSelectedLevel] = useState('all');
  const [selectedCategory, setSelectedCategory] = useState('all');

  const levels = [
    { value: 'all', label: 'All Levels' },
    { value: 'beginner', label: 'Beginner' },
    { value: 'intermediate', label: 'Intermediate' },
    { value: 'advanced', label: 'Advanced' }
  ];

  const categories = [
    { value: 'all', label: 'All Categories' },
    { value: 'programming', label: 'Programming' },
    { value: 'web', label: 'Web Development' },
    { value: 'mobile', label: 'Mobile Development' },
    { value: 'data', label: 'Data Science' }
  ];

  const filteredCourses = listCourses.filter(course => {
    const matchesSearch = course.title.toLowerCase().includes(searchQuery.toLowerCase()) ||
                         course.description.toLowerCase().includes(searchQuery.toLowerCase());
    const matchesLevel = selectedLevel === 'all' || course.level.toLowerCase().includes(selectedLevel);
    const matchesCategory = selectedCategory === 'all';

    return matchesSearch && matchesLevel && matchesCategory;
  });

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      {/* Hero Section */}
      <div className="bg-gradient-to-br from-blue-600 to-blue-800 dark:from-blue-900 dark:to-blue-950">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
          <div className="text-center">
            <h1 className="text-4xl font-bold text-white mb-4">
              Explore Our Courses
            </h1>
            <p className="text-xl text-white/90 mb-8 max-w-2xl mx-auto">
              Learn from our comprehensive collection of courses designed to help you master programming and development skills.
            </p>
            <div className="max-w-2xl mx-auto">
              <div className="relative">
                <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
                <input
                  type="text"
                  placeholder="Search for courses..."
                  value={searchQuery}
                  onChange={(e) => setSearchQuery(e.target.value)}
                  className="w-full pl-10 pr-4 py-3 bg-white/10 backdrop-blur-lg border border-white/20 rounded-lg text-white placeholder-white/60 focus:outline-none focus:ring-2 focus:ring-white/30"
                />
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* Filters Section */}
      <div className="border-b dark:border-gray-800">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
          <div className="flex flex-wrap items-center gap-4">
            <div className="flex items-center">
              <Filter className="h-5 w-5 text-gray-400 mr-2" />
              <span className="text-gray-600 dark:text-gray-400">Filters:</span>
            </div>
            <div className="flex flex-wrap gap-4">
              <div className="relative">
                <select
                  value={selectedLevel}
                  onChange={(e) => setSelectedLevel(e.target.value)}
                  className="appearance-none bg-white dark:bg-gray-800 border border-gray-300 dark:border-gray-700 rounded-lg pl-4 pr-10 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
                >
                  {levels.map(level => (
                    <option key={level.value} value={level.value}>
                      {level.label}
                    </option>
                  ))}
                </select>
                <ChevronDown className="absolute right-3 top-1/2 transform -translate-y-1/2 h-4 w-4 text-gray-400 pointer-events-none" />
              </div>
              <div className="relative">
                <select
                  value={selectedCategory}
                  onChange={(e) => setSelectedCategory(e.target.value)}
                  className="appearance-none bg-white dark:bg-gray-800 border border-gray-300 dark:border-gray-700 rounded-lg pl-4 pr-10 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
                >
                  {categories.map(category => (
                    <option key={category.value} value={category.value}>
                      {category.label}
                    </option>
                  ))}
                </select>
                <ChevronDown className="absolute right-3 top-1/2 transform -translate-y-1/2 h-4 w-4 text-gray-400 pointer-events-none" />
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* Course Grid */}
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          {filteredCourses.map((course) => (
            <div
              key={course.id}
              className="bg-white dark:bg-gray-800 rounded-xl overflow-hidden shadow-sm hover:shadow-lg transition-shadow duration-300 border border-gray-200 dark:border-gray-700"
            >
              <div className="relative">
                <img
                  src={course.image}
                  alt={course.title}
                  className="w-full h-48 object-cover"
                />
                <div className="absolute top-4 right-4 flex items-center bg-white dark:bg-gray-900 rounded-full px-2 py-1">
                  <Star className="h-4 w-4 text-yellow-400 fill-current" />
                  <span className="ml-1 text-sm font-medium">{course.rating}</span>
                </div>
                <div className="absolute bottom-4 left-4">
                  <span className={`${course.color} text-white px-3 py-1 rounded-full text-sm font-medium`}>
                    {course.level}
                  </span>
                </div>
              </div>
              
              <div className="p-6">
                <h3 className="text-xl font-bold text-gray-900 dark:text-white mb-2">
                  {course.title}
                </h3>
                <p className="text-gray-600 dark:text-gray-300 mb-4 line-clamp-2">
                  {course.description}
                </p>
                
                <div className="flex items-center justify-between text-sm text-gray-500 dark:text-gray-400 mb-4">
                  <div className="flex items-center">
                    <Clock className="h-4 w-4 mr-1" />
                    {course.duration}
                  </div>
                  <div className="flex items-center">
                    <Users className="h-4 w-4 mr-1" />
                    {course.students.toLocaleString()} students
                  </div>
                </div>
                
                <a
                  href={`/courses/${course.id}`}
                  className={`block w-full text-center px-4 py-2 ${course.color} text-white rounded-lg hover:opacity-90 transition duration-200 font-medium`}
                >
                  Enroll Now
                </a>
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
};

export default CoursePage;

/== DashboardPage.tsx
import React, { useEffect } from 'react';
import { useAuthStore } from '../store/authStore';
import { useCourseStore } from '../store/courseStore';
import { Link } from 'react-router-dom';
import { ChevronRight, Code, PlayCircle, Award, Users, Plus, BookOpen, Star, Globe, Zap, Crown, Check, AlignCenterVertical as Certificate } from 'lucide-react';
import Header from '../components/organisms/Header';
import { Steps } from 'intro.js-react';
import 'intro.js/introjs.css';

const DashboardPage: React.FC = () => {
  const { user, completeTour } = useAuthStore();
  const { 
    enrolledCourses, 
    recommendedCourses,
    currentStreak,
    highestStreak,
    totalXp,
    solvedChallenges,
    dailyChallenge,
    leaderboard
  } = useCourseStore();

  const [showTour, setShowTour] = React.useState(!user?.hasSeenTour);

  const steps = [
    {
      element: '.dashboard-welcome',
      intro: 'Welcome to your dashboard! Here you can track your progress and see your achievements.',
      position: 'bottom'
    },
    {
      element: '.dashboard-stats',
      intro: 'View your learning statistics including XP earned, challenges solved, and current streak.',
      position: 'bottom'
    },
    {
      element: '.daily-challenge',
      intro: 'Complete daily challenges to earn XP and maintain your learning streak.',
      position: 'top'
    },
    {
      element: '.enrolled-courses',
      intro: 'Access your enrolled courses and continue your learning journey.',
      position: 'top'
    },
    {
      element: '.leaderboard-section',
      intro: 'Compete with other learners and track your ranking on the leaderboard.',
      position: 'left'
    },
    {
      element: '.learning-path',
      intro: 'Follow your personalized learning path to master programming skills.',
      position: 'left'
    }
  ];

  const onTourExit = () => {
    setShowTour(false);
    completeTour();
  };

  if (!user) {
    return (
      <div className="min-h-screen bg-gradient-to-br from-gray-900 to-gray-800 flex items-center justify-center">
        <div className="bg-white/10 backdrop-blur-lg rounded-xl p-8 text-center max-w-md w-full mx-4">
          <h2 className="text-2xl font-bold text-white mb-4">Please sign in to view your dashboard</h2>
          <p className="text-gray-300 mb-6">Sign in to track your progress and continue learning</p>
          <Link
            to="/"
            className="inline-block w-full py-3 px-6 bg-primary-500 text-white font-medium rounded-lg hover:bg-primary-600 transition duration-200"
          >
            Go to Home
          </Link>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 to-gray-800">
      <Header />
      
      <Steps
        enabled={showTour}
        steps={steps}
        initialStep={0}
        onExit={onTourExit}
        options={{
          dontShowAgain: true,
          dontShowAgainLabel: "Don't show this again",
          showProgress: true,
          showBullets: true,
          exitOnOverlayClick: false,
          nextLabel: 'Next →',
          prevLabel: '← Back',
          doneLabel: 'Done',
          overlayOpacity: 0.7
        }}
      />
      
      {/* User Stats Section */}
      <div className="bg-white/10 backdrop-blur-lg border-b border-white/10">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
          <div className="flex flex-col md:flex-row md:items-center md:justify-between">
            <div className="dashboard-welcome">
              <div className="flex items-center mb-2">
                <div className="w-12 h-12 bg-primary-500 rounded-xl flex items-center justify-center text-white text-xl font-bold">
                  {user.name.charAt(0)}
                </div>
                <div className="ml-4">
                  <h1 className="text-2xl font-bold text-white">Welcome back, {user.name}!</h1>
                  <p className="text-gray-300">Continue your learning journey</p>
                </div>
              </div>
              <div className="flex items-center mt-4 space-x-6">
                <div className="flex items-center text-yellow-400">
                  <Star className="h-5 w-5 fill-current" />
                  <span className="ml-2 font-medium">{totalXp} XP</span>
                </div>
                <div className="flex items-center text-blue-400">
                  <Zap className="h-5 w-5" />
                  <span className="ml-2 font-medium">{currentStreak} day streak</span>
                </div>
              </div>
            </div>
            <div className="mt-6 md:mt-0 flex space-x-4">
              <Link
                to="/compiler"
                className="inline-flex items-center px-4 py-2 bg-white/10 text-white rounded-lg hover:bg-white/20 transition duration-200"
              >
                <Code className="h-5 w-5 mr-2" />
                Open Compiler
              </Link>
              <Link
                to="/courses"
                className="inline-flex items-center px-4 py-2 bg-primary-500 text-white rounded-lg hover:bg-primary-600 transition duration-200"
              >
                <Plus className="h-5 w-5 mr-2" />
                Explore Courses
              </Link>
            </div>
          </div>
        </div>
      </div>

      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
          {/* Main Content */}
          <div className="lg:col-span-2 space-y-8">
            {/* Stats Overview */}
            <div className="dashboard-stats grid grid-cols-2 md:grid-cols-4 gap-4">
              <div className="bg-gradient-to-br from-blue-500 to-blue-600 rounded-xl p-6 text-white">
                <div className="flex items-center mb-2">
                  <Award className="h-8 w-8" />
                </div>
                <div className="text-2xl font-bold">{totalXp}</div>
                <div className="text-sm text-white/80">Total XP</div>
              </div>
              <div className="bg-gradient-to-br from-green-500 to-green-600 rounded-xl p-6 text-white">
                <div className="flex items-center mb-2">
                  <PlayCircle className="h-8 w-8" />
                </div>
                <div className="text-2xl font-bold">{solvedChallenges}</div>
                <div className="text-sm text-white/80">Challenges Solved</div>
              </div>
              <div className="bg-gradient-to-br from-purple-500 to-purple-600 rounded-xl p-6 text-white">
                <div className="flex items-center mb-2">
                  <Users className="h-8 w-8" />
                </div>
                <div className="text-2xl font-bold">{currentStreak}</div>
                <div className="text-sm text-white/80">Day Streak</div>
              </div>
              <Link 
                to="/certificates"
                className="bg-gradient-to-br from-yellow-500 to-yellow-600 rounded-xl p-6 text-white hover:from-yellow-600 hover:to-yellow-700 transition-all duration-300"
              >
                <div className="flex items-center mb-2">
                  <Award className="h-8 w-8" />
                </div>
                <div className="text-2xl font-bold">View</div>
                <div className="text-sm text-white/80">Certificates</div>
              </Link>
            </div>

            {/* Daily Challenge */}
            {dailyChallenge && (
              <div className="daily-challenge bg-gradient-to-r from-indigo-500 to-purple-600 rounded-xl p-6 text-white">
                <div className="flex items-center justify-between mb-4">
                  <div className="flex items-center">
                    <div className="w-12 h-12 bg-white/10 rounded-xl flex items-center justify-center">
                      <PlayCircle className="h-6 w-6" />
                    </div>
                    <div className="ml-4">
                      <h3 className="text-xl font-bold">Daily Challenge</h3>
                      <p className="text-white/80">{dailyChallenge.description}</p>
                    </div>
                  </div>
                  <button className="px-4 py-2 bg-white/10 rounded-lg hover:bg-white/20 transition duration-200">
                    Start Challenge
                  </button>
                </div>
                <div className="flex items-center text-sm mt-4 pt-4 border-t border-white/10">
                  <Star className="h-4 w-4 mr-2" />
                  <span>{dailyChallenge.xpPoints} XP</span>
                  <span className="mx-2">•</span>
                  <span className="capitalize">{dailyChallenge.difficulty} Difficulty</span>
                </div>
              </div>
            )}

            {/* Enrolled Courses */}
            {enrolledCourses.length > 0 && (
              <div className="enrolled-courses">
                <div className="flex items-center justify-between mb-6">
                  <h2 className="text-xl font-bold text-white">My Courses</h2>
                  <Link
                    to="/my-learning"
                    className="text-primary-400 hover:text-primary-300 flex items-center"
                  >
                    View All
                    <ChevronRight className="h-4 w-4 ml-1" />
                  </Link>
                </div>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  {enrolledCourses.map((course) => (
                    <div
                      key={course.id}
                      className="bg-white/10 backdrop-blur-lg rounded-xl p-6 hover:bg-white/20 transition duration-300"
                    >
                      <div className="flex items-center mb-4">
                        <div className={`w-12 h-12 ${course.color} rounded-xl flex items-center justify-center`}>
                          <BookOpen className="h-6 w-6 text-white" />
                        </div>
                        <div className="ml-4">
                          <h3 className="font-bold text-white">{course.title}</h3>
                          <div className="mt-2 w-32">
                            <div className="h-1.5 bg-white/20 rounded-full">
                              <div
                                className="h-1.5 bg-primary-500 rounded-full"
                                style={{ width: `${course.progress}%` }}
                              />
                            </div>
                          </div>
                        </div>
                      </div>
                      <div className="grid grid-cols-2 gap-4 mb-4">
                        <div className="text-center p-3 bg-white/5 rounded-lg">
                          <div className="text-sm font-medium text-white">
                            {course.solvedProblems}/{course.totalProblems}
                          </div>
                          <div className="text-xs text-gray-400">Problems</div>
                        </div>
                        <div className="text-center p-3 bg-white/5 rounded-lg">
                          <div className="text-sm font-medium text-white">
                            {course.solvedQuizzes}/{course.totalQuizzes}
                          </div>
                          <div className="text-xs text-gray-400">Quizzes</div>
                        </div>
                      </div>
                      <Link
                        to={`/courses/${course.id}/lessons/${course.firstLessonId}`}
                        className="block w-full py-2 px-4 bg-primary-500 text-white text-center rounded-lg hover:bg-primary-600 transition duration-200"
                      >
                        Continue Learning
                      </Link>
                    </div>
                  ))}
                </div>
              </div>
            )}
          </div>

          {/* Sidebar */}
          <div className="space-y-8">
            {/* Leaderboard */}
            <div className="leaderboard-section bg-white/10 backdrop-blur-lg rounded-xl p-6">
              <h3 className="text-xl font-bold text-white mb-6">Leaderboard</h3>
              <div className="space-y-4">
                {leaderboard.slice(0, 5).map((entry, index) => (
                  <div
                    key={entry.id}
                    className={`flex items-center justify-between p-3 rounded-lg ${
                      index === 0 ? 'bg-yellow-500/20' : 'hover:bg-white/5'
                    }`}
                  >
                    <div className="flex items-center">
                      <div className="w-8 text-center font-medium text-gray-400">
                        {index + 1}
                      </div>
                      <img
                        src={entry.avatar}
                        alt={entry.name}
                        className="w-8 h-8 rounded-lg mx-3"
                      />
                      <div>
                        <div className="font-medium text-white text-sm">
                          {entry.name}
                        </div>
                        <div className="text-xs text-gray-400">
                          {entry.xp} XP
                        </div>
                      </div>
                    </div>
                    {index === 0 && (
                      <div className="w-6 h-6 bg-yellow-500/20 rounded-lg flex items-center justify-center">
                        <Crown className="h-4 w-4 text-yellow-500" />
                      </div>
                    )}
                  </div>
                ))}
              </div>
              <Link
                to="/leaderboard"
                className="block w-full text-center mt-6 py-2 px-4 bg-white/10 text-white rounded-lg hover:bg-white/20 transition duration-200"
              >
                View Full Leaderboard
              </Link>
            </div>

            {/* Learning Path */}
            <div className="learning-path bg-white/10 backdrop-blur-lg rounded-xl p-6">
              <h3 className="text-xl font-bold text-white mb-6">Learning Path</h3>
              <div className="relative">
                <div className="absolute left-4 top-0 bottom-0 w-0.5 bg-white/10"></div>
                <div className="space-y-6">
                  <div className="relative pl-10">
                    <div className="absolute left-2 w-5 h-5 rounded-lg bg-green-500 flex items-center justify-center">
                      <Check className="h-3 w-3 text-white" />
                    </div>
                    <h4 className="font-medium text-white">Python Basics</h4>
                    <p className="text-sm text-gray-400">Completed</p>
                  </div>
                  <div className="relative pl-10">
                    <div className="absolute left-2 w-5 h-5 rounded-lg bg-blue-500 flex items-center justify-center">
                      <span className="text-white text-xs">2</span>
                    </div>
                    <h4 className="font-medium text-white">Data Structures</h4>
                    <p className="text-sm text-gray-400">In Progress - 60%</p>
                  </div>
                  <div className="relative pl-10">
                    <div className="absolute left-2 w-5 h-5 rounded-lg bg-white/10 flex items-center justify-center">
                      <span className="text-gray-400 text-xs">3</span>
                    </div>
                    <h4 className="font-medium text-gray-400">Algorithms</h4>
                    <p className="text-sm text-gray-500">Locked</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default DashboardPage;

/== HomePage.tsx
import React from 'react';
import { useAuthStore } from '../store/authStore';
import { BookOpen, Code, Terminal, Award, Users, Laptop, Star, Globe, Zap, ArrowRight, CheckCircle, Rocket, Brain } from 'lucide-react';
import Button from '../components/atoms/Button';
import Card from '../components/atoms/Card';

const HomePage = () => {
  const { isAuthenticated } = useAuthStore();

  const stats = [
    { value: '1M+', label: 'Active Learners', icon: <Users className="h-8 w-8 text-brand-green" /> },
    { value: '200+', label: 'Tutorials', icon: <BookOpen className="h-8 w-8 text-brand-green" /> },
    { value: '50+', label: 'Practice Projects', icon: <Code className="h-8 w-8 text-brand-green" /> },
    { value: '24/7', label: 'Support', icon: <Globe className="h-8 w-8 text-brand-green" /> },
  ];

  const features = [
    {
      icon: Brain,
      title: 'Smart Learning Path',
      description: 'Personalized learning journey tailored to your goals and skill level.',
      color: 'bg-gradient-to-br from-emerald-400/90 to-green-500/90'
    },
    {
      icon: Terminal,
      title: 'Interactive Coding',
      description: 'Write, compile, and run code directly in your browser with real-time feedback.',
      color: 'bg-gradient-to-br from-cyan-400/90 to-blue-500/90'
    },
    {
      icon: CheckCircle,
      title: 'Hands-on Practice',
      description: 'Learn by doing with practical exercises and real-world projects.',
      color: 'bg-gradient-to-br from-violet-400/90 to-purple-500/90'
    }
  ];

  const testimonials = [
    {
      name: "Sarah Chen",
      role: "Software Developer",
      image: "https://images.unsplash.com/photo-1494790108377-be9c29b29330?ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80",
      content: "Coder Growth transformed my learning journey. The structured approach and hands-on projects helped me land my dream job."
    },
    {
      name: "Michael Rodriguez",
      role: "Student",
      image: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80",
      content: "The interactive lessons and community support made learning to code enjoyable and effective."
    },
    {
      name: "David Kim",
      role: "Tech Lead",
      image: "https://images.unsplash.com/photo-1500648767791-00dcc994a43e?ixlib=rb-1.2.1&auto=format&fit=crop&w=200&q=80",
      content: "As a mentor, I recommend Coder Growth to all aspiring developers. The curriculum is comprehensive and up-to-date."
    }
  ];

  return (
    <div className="min-h-screen">
      {/* Hero Section */}
      <div className="relative overflow-hidden bg-gray-900">
        <div className="absolute inset-0 bg-grid-pattern opacity-10"></div>
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24 relative">
          <div className="text-center">
            <h1 className="text-4xl md:text-6xl font-bold text-white mb-6 leading-tight">
              Master Programming with
              <span className="text-brand-green"> Hands-on Practice</span>
            </h1>
            <p className="text-xl text-gray-300 mb-8 max-w-3xl mx-auto">
              Join millions of learners worldwide and start your coding journey today.
              Interactive tutorials, real-time feedback, and expert-led courses.
            </p>
            <div className="flex flex-col sm:flex-row justify-center gap-4">
              <Button
                variant="primary"
                href={isAuthenticated ? '/dashboard' : '/courses'}
                className="bg-brand-green hover:bg-brand-green/90 text-lg px-8 py-4"
              >
                {isAuthenticated ? 'Continue Learning' : 'Start Learning'} <ArrowRight className="ml-2" />
              </Button>
              <Button
                variant="outline"
                href="/compiler"
                className="border-white text-white hover:bg-white/10 text-lg px-8 py-4"
              >
                Try Compiler <Terminal className="ml-2" />
              </Button>
            </div>
          </div>
        </div>
      </div>

      {/* Stats Section */}
      <div className="bg-white py-16">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="grid grid-cols-2 md:grid-cols-4 gap-8">
            {stats.map((stat, index) => (
              <div key={index} className="text-center">
                <div className="inline-flex items-center justify-center w-12 h-12 rounded-xl bg-brand-green/10 mb-4">
                  {stat.icon}
                </div>
                <div className="text-3xl font-bold text-gray-900 mb-2">{stat.value}</div>
                <div className="text-gray-600">{stat.label}</div>
              </div>
            ))}
          </div>
        </div>
      </div>

      {/* Features Section */}
      <div className="bg-gray-50 py-24">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-4xl font-bold text-gray-900 mb-4">
              Why Choose Coder Growth?
            </h2>
            <p className="text-xl text-gray-600 max-w-3xl mx-auto">
              Our platform provides all the tools and resources you need to become a successful programmer.
            </p>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
            {features.map((feature, index) => (
              <Card 
                key={index} 
                className="p-8 transform hover:scale-105 transition-all duration-300 bg-white border border-gray-100 hover:shadow-xl"
              >
                <div className={`w-16 h-16 ${feature.color} rounded-2xl flex items-center justify-center mb-6`}>
                  <feature.icon className="h-8 w-8 text-white" />
                </div>
                <h3 className="text-xl font-bold text-gray-800 mb-4">{feature.title}</h3>
                <p className="text-gray-600 leading-relaxed">{feature.description}</p>
              </Card>
            ))}
          </div>
        </div>
      </div>

      {/* Testimonials Section */}
      <div className="bg-white py-24">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-4xl font-bold text-gray-900 mb-4">
              What Our Students Say
            </h2>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
            {testimonials.map((testimonial, index) => (
              <Card 
                key={index} 
                className="p-8 bg-gradient-to-br from-gray-50 to-white border border-gray-100 hover:shadow-xl transition-all duration-300"
              >
                <div className="flex items-center mb-6">
                  <img
                    src={testimonial.image}
                    alt={testimonial.name}
                    className="w-12 h-12 rounded-full mr-4 border-2 border-brand-green/20"
                  />
                  <div>
                    <h4 className="font-bold text-gray-900">{testimonial.name}</h4>
                    <p className="text-brand-green text-sm">{testimonial.role}</p>
                  </div>
                </div>
                <p className="text-gray-700 leading-relaxed">"{testimonial.content}"</p>
              </Card>
            ))}
          </div>
        </div>
      </div>

      {/* CTA Section */}
      <div className="bg-brand-green py-24 relative overflow-hidden">
        <div className="absolute inset-0 bg-grid-pattern opacity-10"></div>
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative">
          <div className="text-center">
            <h2 className="text-4xl font-bold text-white mb-6">
              Ready to Start Your Coding Journey?
            </h2>
            <p className="text-xl text-white/90 mb-8 max-w-2xl mx-auto">
              Join our community of learners and start building your programming skills today.
              No credit card required.
            </p>
            <Button
              variant="primary"
              href="/courses"
              className="bg-brand-green hover:bg-brand-green/90 text-lg px-8 py-4"
            >
              Get Started for Free <Rocket className="ml-2" />
            </Button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default HomePage;

/== LeaderboardPage.tsx
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import Button from '../components/atoms/Button';

interface LeaderboardUser {
  id: string;
  name: string;
  avatarUrl: string;
  points: number;
  rank: number;
  coursesCompleted: number;
  streak: number;
  badges: string[];
  isCurrentUser: boolean;
}

interface LeaderboardCategory {
  id: string;
  name: string;
  description: string;
}

const LeaderboardPage: React.FC = () => {
  const [isLoading, setIsLoading] = useState(true);
  const [timeframe, setTimeframe] = useState<'weekly' | 'monthly' | 'allTime'>('weekly');
  const [category, setCategory] = useState<string>('all');
  const [leaderboardUsers, setLeaderboardUsers] = useState<LeaderboardUser[]>([]);
  const [categories, setCategories] = useState<LeaderboardCategory[]>([]);
  const [currentUserRank, setCurrentUserRank] = useState<number | null>(null);
  
  // Simulate fetching data
  useEffect(() => {
    const fetchLeaderboardData = async () => {
      setIsLoading(true);
      
      // Simulate API delay
      await new Promise(resolve => setTimeout(resolve, 1000));
      
      // Mock categories
      const mockCategories = [
        { id: 'all', name: 'All Categories', description: 'Overall performance across all learning categories' },
        { id: 'programming', name: 'Programming', description: 'Coding and development challenges' },
        { id: 'design', name: 'Design', description: 'UI/UX and graphic design courses' },
        { id: 'business', name: 'Business', description: 'Management and entrepreneurship' },
        { id: 'data', name: 'Data Science', description: 'Analytics, machine learning, and data visualization' }
      ];
      
      // Generate mock leaderboard data
      const numberOfUsers = 20;
      const avatarStyles = [
        'adventurer', 'adventurer-neutral', 'avataaars', 'big-ears', 
        'big-smile', 'bottts', 'croodles', 'identicon', 'initials', 'micah'
      ];
      
      const generateRandomBadges = () => {
        const allBadges = ['Early Bird', 'Fast Learner', 'Perfectionist', 'Team Player', 'Problem Solver', 'Night Owl', 'Consistent', 'Top Contributor'];
        const count = Math.floor(Math.random() * 3); // 0-2 badges
        const badges: string[] = [];
        
        for (let i = 0; i < count; i++) {
          const randomBadge = allBadges[Math.floor(Math.random() * allBadges.length)];
          if (!badges.includes(randomBadge)) {
            badges.push(randomBadge);
          }
        }
        
        return badges;
      };
      
      // Generate users with descending points
      let mockUsers = Array.from({ length: numberOfUsers }, (_, i) => {
        // First rank is the highest points
        const pointsBase = 1000 - (i * (1000 / numberOfUsers));
        // Add some randomness
        const points = Math.round(pointsBase + (Math.random() * 50) - 25);
        
        // Randomly select one user to be the current user
        const isCurrentUser = i === 5; // User is ranked 6th
        
        if (isCurrentUser) {
          setCurrentUserRank(i + 1);
        }
        
        return {
          id: `user-${i + 1}`,
          name: `User ${i + 1}`,
          avatarUrl: `https://api.dicebear.com/7.x/${avatarStyles[i % avatarStyles.length]}/svg?seed=${i}`,
          points,
          rank: i + 1,
          coursesCompleted: Math.floor(Math.random() * 20) + 1,
          streak: Math.floor(Math.random() * 30) + 1,
          badges: generateRandomBadges(),
          isCurrentUser
        };
      });
      
      // Sort by points (descending)
      mockUsers.sort((a, b) => b.points - a.points);
      
      // Update ranks after sorting
      mockUsers = mockUsers.map((user, index) => ({
        ...user,
        rank: index + 1
      }));
      
      setCategories(mockCategories);
      setLeaderboardUsers(mockUsers);
      setIsLoading(false);
    };
    
    fetchLeaderboardData();
  }, [timeframe, category]);
  
  const getTimeframeLabel = () => {
    switch (timeframe) {
      case 'weekly': return 'This Week';
      case 'monthly': return 'This Month';
      case 'allTime': return 'All Time';
      default: return '';
    }
  };
  
  const currentUser = leaderboardUsers.find(user => user.isCurrentUser);
  
  // Utility function to format large numbers
  const formatNumber = (num: number) => {
    return new Intl.NumberFormat().format(num);
  };
  
  if (isLoading) {
    return (
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="animate-pulse space-y-8">
          <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-1/4"></div>
          <div className="h-6 bg-gray-200 dark:bg-gray-700 rounded w-1/2"></div>
          
          {/* Tab skeleton */}
          <div className="flex space-x-2">
            <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-24"></div>
            <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-24 opacity-60"></div>
            <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-24 opacity-60"></div>
          </div>
          
          {/* Category selector skeleton */}
          <div className="h-12 bg-gray-200 dark:bg-gray-700 rounded w-full md:w-1/3"></div>
          
          {/* Leaderboard table skeleton */}
          <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden">
            <div className="h-14 bg-gray-100 dark:bg-gray-700"></div>
            {[1, 2, 3, 4, 5].map(i => (
              <div key={i} className="h-20 border-t border-gray-200 dark:border-gray-700 flex">
                <div className="w-16 bg-gray-200 dark:bg-gray-700 m-2"></div>
                <div className="flex-1 py-4 px-2">
                  <div className="h-4 bg-gray-200 dark:bg-gray-700 rounded w-1/3 mb-2"></div>
                  <div className="h-3 bg-gray-200 dark:bg-gray-700 rounded w-1/4"></div>
                </div>
                <div className="w-24 bg-gray-200 dark:bg-gray-700 m-2"></div>
              </div>
            ))}
          </div>
        </div>
      </div>
    );
  }
  
  return (
    <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      {/* Header */}
      <div className="mb-8">
        <h1 className="text-3xl font-bold text-gray-900 dark:text-white mb-2">Leaderboard</h1>
        <p className="text-lg text-gray-600 dark:text-gray-400">
          See how you stack up against other learners and compete for the top spots
        </p>
      </div>
      
      {/* Current user stats summary */}
      {currentUser && (
        <div className="mb-8 bg-gradient-to-r from-blue-600 to-indigo-600 dark:from-blue-500 dark:to-indigo-500 rounded-xl shadow-md overflow-hidden">
          <div className="p-6 md:p-8">
            <div className="md:flex items-center">
              <div className="flex-shrink-0 mb-4 md:mb-0 md:mr-6">
                <div className="relative">
                  <img 
                    src={currentUser.avatarUrl} 
                    alt={currentUser.name}
                    className="w-20 h-20 rounded-full border-4 border-white dark:border-gray-800 object-cover bg-white" 
                  />
                  <div className="absolute -bottom-2 -right-2 flex items-center justify-center w-10 h-10 rounded-full bg-white dark:bg-gray-800 border-2 border-blue-600 dark:border-blue-500">
                    <span className="text-xl font-bold text-blue-600 dark:text-blue-400">
                      #{currentUser.rank}
                    </span>
                  </div>
                </div>
              </div>
              
              <div className="flex-1">
                <h2 className="text-xl font-bold text-white mb-1">
                  {currentUser.name} <span className="text-blue-200">(You)</span>
                </h2>
                <p className="text-blue-100 mb-4">You're currently ranked #{currentUser.rank} on the leaderboard</p>
                
                <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
                  <div className="bg-white/10 rounded-lg p-3">
                    <p className="text-sm text-blue-100">Total Points</p>
                    <p className="text-2xl font-bold text-white">{formatNumber(currentUser.points)}</p>
                  </div>
                  <div className="bg-white/10 rounded-lg p-3">
                    <p className="text-sm text-blue-100">Courses Completed</p>
                    <p className="text-2xl font-bold text-white">{currentUser.coursesCompleted}</p>
                  </div>
                  <div className="bg-white/10 rounded-lg p-3">
                    <p className="text-sm text-blue-100">Day Streak</p>
                    <p className="text-2xl font-bold text-white">{currentUser.streak} days</p>
                  </div>
                  <div className="bg-white/10 rounded-lg p-3">
                    <p className="text-sm text-blue-100">Rank Progress</p>
                    <div className="flex items-center space-x-2">
                      <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M5 10l7-7m0 0l7 7m-7-7v18" />
                      </svg>
                      <p className="text-xl font-bold text-white">+3 this week</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      )}
      
      {/* Timeframe selector */}
      <div className="mb-6">
        <div className="flex flex-wrap space-x-2 mb-6">
          <button
            onClick={() => setTimeframe('weekly')}
            className={`py-2 px-4 rounded-lg font-medium text-sm transition-colors duration-200 ${
              timeframe === 'weekly'
                ? 'bg-blue-600 dark:bg-blue-500 text-white'
                : 'bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-300 hover:bg-gray-200 dark:hover:bg-gray-700'
            }`}
          >
            This Week
          </button>
          <button
            onClick={() => setTimeframe('monthly')}
            className={`py-2 px-4 rounded-lg font-medium text-sm transition-colors duration-200 ${
              timeframe === 'monthly'
                ? 'bg-blue-600 dark:bg-blue-500 text-white'
                : 'bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-300 hover:bg-gray-200 dark:hover:bg-gray-700'
            }`}
          >
            This Month
          </button>
          <button
            onClick={() => setTimeframe('allTime')}
            className={`py-2 px-4 rounded-lg font-medium text-sm transition-colors duration-200 ${
              timeframe === 'allTime'
                ? 'bg-blue-600 dark:bg-blue-500 text-white'
                : 'bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-300 hover:bg-gray-200 dark:hover:bg-gray-700'
            }`}
          >
            All Time
          </button>
        </div>
        
        {/* Category selector */}
        <div className="mb-6">
          <select
            value={category}
            onChange={(e) => setCategory(e.target.value)}
            className="block w-full md:w-1/3 px-4 py-3 text-base rounded-lg bg-white dark:bg-gray-800 border border-gray-300 dark:border-gray-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:text-white"
          >
            {categories.map(cat => (
              <option key={cat.id} value={cat.id}>
                {cat.name}
              </option>
            ))}
          </select>
          <p className="mt-2 text-sm text-gray-500 dark:text-gray-400">
            {categories.find(c => c.id === category)?.description}
          </p>
        </div>
      </div>
      
      {/* Leaderboard */}
      <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden">
        {/* Table header */}
        <div className="bg-gray-50 dark:bg-gray-900/50 border-b border-gray-200 dark:border-gray-700 px-6 py-4">
          <h3 className="text-lg font-semibold text-gray-900 dark:text-white">
            Top Learners - {getTimeframeLabel()}
          </h3>
        </div>
        
        {/* Table body */}
        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {leaderboardUsers.slice(0, 10).map((user) => (
            <div 
              key={user.id}
              className={`flex items-center py-4 px-6 transition-colors duration-150 ${
                user.isCurrentUser ? 'bg-blue-50 dark:bg-blue-900/20' : 'hover:bg-gray-50 dark:hover:bg-gray-750'
              }`}
            >
              {/* Rank */}
              <div className="flex-shrink-0 w-12 text-center mr-4">
                {user.rank <= 3 ? (
                  <div className={`inline-flex items-center justify-center w-8 h-8 rounded-full
                    ${user.rank === 1 ? 'bg-yellow-100 dark:bg-yellow-900/30 text-yellow-600 dark:text-yellow-400' : 
                      user.rank === 2 ? 'bg-gray-100 dark:bg-gray-700 text-gray-600 dark:text-gray-300' : 
                      'bg-amber-100 dark:bg-amber-900/30 text-amber-600 dark:text-amber-400'}`}>
                    <span className="text-lg font-bold">{user.rank}</span>
                  </div>
                ) : (
                  <span className={`text-lg font-medium ${
                    user.isCurrentUser ? 'text-blue-600 dark:text-blue-400' : 'text-gray-500 dark:text-gray-400'
                  }`}>
                    {user.rank}
                  </span>
                )}
              </div>
              
              {/* User info */}
              <div className="flex items-center flex-1">
                <div className="flex-shrink-0 relative">
                  <img 
                    src={user.avatarUrl} 
                    alt={user.name} 
                    className={`w-12 h-12 rounded-full object-cover bg-white border 
                      ${user.isCurrentUser ? 'border-blue-500 dark:border-blue-400' : 'border-gray-200 dark:border-gray-700'}`} 
                  />
                  {user.rank <= 3 && (
                    <div className={`absolute -top-1 -right-1 w-5 h-5 flex items-center justify-center text-white rounded-full
                      ${user.rank === 1 ? 'bg-yellow-500 dark:bg-yellow-400' : 
                        user.rank === 2 ? 'bg-gray-400 dark:bg-gray-300' : 
                        'bg-amber-500 dark:bg-amber-400'}`}>
                      <svg xmlns="http://www.w3.org/2000/svg" className="h-3 w-3" viewBox="0 0 20 20" fill="currentColor">
                        <path fillRule="evenodd" d="M5 2a1 1 0 011 1v1h1a1 1 0 010 2H6v1a1 1 0 01-2 0V6H3a1 1 0 010-2h1V3a1 1 0 011-1zm0 10a1 1 0 011 1v1h1a1 1 0 110 2H6v1a1 1 0 11-2 0v-1H3a1 1 0 110-2h1v-1a1 1 0 011-1zM12 2a1 1 0 01.967.744L14.146 7.2 17.5 9.134a1 1 0 010 1.732l-3.354 1.935-1.18 4.455a1 1 0 01-1.933 0L9.854 12.8 6.5 10.866a1 1 0 010-1.732l3.354-1.935 1.18-4.455A1 1 0 0112 2z" clipRule="evenodd" />
                      </svg>
                    </div>
                  )}
                </div>
                
                <div className="ml-4">
                  <div className="font-medium text-gray-900 dark:text-white flex items-center">
                    {user.name}
                    {user.isCurrentUser && (
                      <span className="ml-2 text-xs bg-blue-100 dark:bg-blue-900 text-blue-800 dark:text-blue-200 px-2 py-0.5 rounded-full">
                        You
                      </span>
                    )}
                  </div>
                  
                  <div className="flex items-center mt-1">
                    <div className="flex space-x-1 mr-3">
                      <svg className="w-4 h-4 text-blue-500 dark:text-blue-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                        <path d="M9 4.804A7.968 7.968 0 005.5 4c-1.255 0-2.443.29-3.5.804v10A7.969 7.969 0 015.5 14c1.669 0 3.218.51 4.5 1.385A7.962 7.962 0 0114.5 14c1.255 0 2.443.29 3.5.804v-10A7.968 7.968 0 0014.5 4c-1.255 0-2.443.29-3.5.804V12a1 1 0 11-2 0V4.804z" />
                      </svg>
                      <span className="text-sm text-gray-500 dark:text-gray-400">{user.coursesCompleted}</span>
                    </div>
                    
                    <div className="flex space-x-1">
                      <svg className="w-4 h-4 text-red-500 dark:text-red-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                        <path fillRule="evenodd" d="M12.395 2.553a1 1 0 00-1.45-.385c-.345.23-.614.558-.822.88-.214.33-.403.713-.57 1.116-.334.804-.614 1.768-.84 2.734a31.365 31.365 0 00-.613 3.58 2.64 2.64 0 01-.945-1.067c-.328-.68-.398-1.534-.398-2.654A1 1 0 005.05 6.05 6.981 6.981 0 003 11a7 7 0 1011.95-4.95c-.592-.591-.98-.985-1.348-1.467-.363-.476-.724-1.063-1.207-2.03zM12.12 15.12A3 3 0 017 13s.879.5 2.5.5c0-1 .5-4 1.25-4.5.5 1 .786 1.293 1.371 1.879A2.99 2.99 0 0113 13a2.99 2.99 0 01-.879 2.121z" clipRule="evenodd" />
                      </svg>
                      <span className="text-sm text-gray-500 dark:text-gray-400">{user.streak} day streak</span>
                    </div>
                    
                    {user.badges.length > 0 && (
                      <div className="ml-3 flex space-x-1">
                        {user.badges.map((badge, index) => (
                          <span 
                            key={index}
                            className="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-purple-100 text-purple-800 dark:bg-purple-900 dark:text-purple-200"
                          >
                            {badge}
                          </span>
                        ))}
                      </div>
                    )}
                  </div>
                </div>
              </div>
              
              {/* Points */}
              <div className="flex-shrink-0 text-right">
                <div className="font-bold text-2xl text-gray-900 dark:text-white">
                  {formatNumber(user.points)}
                </div>
                <div className="text-xs text-gray-500 dark:text-gray-400">points</div>
              </div>
            </div>
          ))}
        </div>
        
        {/* See more */}
        <div className="bg-gray-50 dark:bg-gray-900/50 border-t border-gray-200 dark:border-gray-700 px-6 py-4 text-center">
          <Button variant="outline" className="text-gray-700 dark:text-gray-300 border-gray-300 dark:border-gray-600 hover:bg-gray-100 dark:hover:bg-gray-700">
            View Full Leaderboard
          </Button>
        </div>
      </div>
      
      {/* How to earn points */}
      <div className="mt-8 bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden">
        <div className="bg-gray-50 dark:bg-gray-900/50 border-b border-gray-200 dark:border-gray-700 px-6 py-4">
          <h3 className="text-lg font-semibold text-gray-900 dark:text-white">
            How to Earn Points
          </h3>
        </div>
        
        <div className="p-6">
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div className="bg-gray-50 dark:bg-gray-900/30 rounded-lg p-4 border border-gray-200 dark:border-gray-700">
              <div className="flex items-center mb-3">
                <div className="flex-shrink-0 bg-green-100 dark:bg-green-900/30 p-2 rounded-lg mr-3">
                  <svg className="w-6 h-6 text-green-600 dark:text-green-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                </div>
                <h4 className="text-lg font-medium text-gray-900 dark:text-white">Complete Courses</h4>
              </div>
              <p className="text-gray-600 dark:text-gray-400">
                Earn 100 points for each course you complete. Bonus points for completing courses within a bundle.
              </p>
            </div>
            
            <div className="bg-gray-50 dark:bg-gray-900/30 rounded-lg p-4 border border-gray-200 dark:border-gray-700">
              <div className="flex items-center mb-3">
                <div className="flex-shrink-0 bg-blue-100 dark:bg-blue-900/30 p-2 rounded-lg mr-3">
                  <svg className="w-6 h-6 text-blue-600 dark:text-blue-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                </div>
                <h4 className="text-lg font-medium text-gray-900 dark:text-white">Daily Streaks</h4>
              </div>
              <p className="text-gray-600 dark:text-gray-400">
                Log in and learn daily to build your streak. Every 7 days, you'll earn bonus points (10, 25, 50).
              </p>
            </div>
            
            <div className="bg-gray-50 dark:bg-gray-900/30 rounded-lg p-4 border border-gray-200 dark:border-gray-700">
              <div className="flex items-center mb-3">
                <div className="flex-shrink-0 bg-purple-100 dark:bg-purple-900/30 p-2 rounded-lg mr-3">
                  <svg className="w-6 h-6 text-purple-600 dark:text-purple-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                  </svg>
                </div>
                <h4 className="text-lg font-medium text-gray-900 dark:text-white">Earn Achievements</h4>
              </div>
              <p className="text-gray-600 dark:text-gray-400">
                Complete special challenges to unlock badges and earn bonus points. Each badge has different point values.
              </p>
            </div>
          </div>
        </div>
      </div>
      
      {/* Back to learning */}
      <div className="mt-8 text-center">
        <Link 
          to="/my-learning" 
          className="inline-flex items-center text-blue-600 dark:text-blue-400 hover:text-blue-800 dark:hover:text-blue-300 font-medium"
        >
          <svg className="mr-1 w-4 h-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 19l-7-7 7-7" />
          </svg>
          Back to My Learning
        </Link>
      </div>
    </div>
  );
};

export default LeaderboardPage;

/== LearningPage.tsx
import React, { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { useLearningStore } from '../store/learningStore';
import { Play, Download, Copy, Trash, Maximize2, Code, ChevronLeft, ChevronRight } from 'lucide-react';
import Logo from '../components/atoms/Logo';

const LearningPage: React.FC = () => {
  const { courseId, lessonId } = useParams<{ courseId: string; lessonId: string }>();
  const navigate = useNavigate();
  const { getCurrentContent, currentStep, setCurrentStep } = useLearningStore();
  
  const [code, setCode] = useState('');
  const [output, setOutput] = useState('');
  const [isRunning, setIsRunning] = useState(false);
  const [selectedOption, setSelectedOption] = useState<string | null>(null);
  const [isAnswerCorrect, setIsAnswerCorrect] = useState<boolean | null>(null);
  const [showSolution, setShowSolution] = useState(false);
  const [testPassed, setTestPassed] = useState(false);
  const [consoleExpanded, setConsoleExpanded] = useState(false);
  
  const content = getCurrentContent(courseId || '', lessonId || '');
  const currentStepContent = content?.steps[currentStep - 1];
  const totalSteps = content?.steps?.length || 0;
  
  useEffect(() => {
    if (content?.steps && content.steps.length > 0) {
      setCurrentStep(1);
      setOutput('');
      setSelectedOption(null);
      setIsAnswerCorrect(null);
      setShowSolution(false);
      setTestPassed(false);
      
      if (content.steps[0].code) {
        setCode(content.steps[0].code);
      }
    }
  }, [lessonId, content]);

  const handleRunCode = () => {
    setIsRunning(true);
    setOutput('');
    
    setTimeout(() => {
      try {
        if (currentStepContent?.type === 'practice') {
          const expectedOutput = currentStepContent.expectedOutput || '';
          
          if (code.includes(`print("${expectedOutput}")`) || code.includes(`print('${expectedOutput}')`)) {
            setOutput(expectedOutput);
            setTestPassed(true);
          } else {
            setOutput('Your output does not match the expected output. Try again!');
            setTestPassed(false);
          }
        } else {
          const result = evaluateCode(code);
          setOutput(result);
        }
      } catch (error) {
        setOutput(`Error: ${error}`);
      }
      
      setIsRunning(false);
    }, 1000);
  };

  const handleCopyCode = () => {
    navigator.clipboard.writeText(code);
  };

  const handleClearCode = () => {
    if (currentStepContent?.code) {
      setCode(currentStepContent.code);
    }
    setOutput('');
    setTestPassed(false);
  };

  const handleDownloadCode = () => {
    const blob = new Blob([code], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'code.go';
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  };

  const handleOptionSelect = (optionId: string) => {
    setSelectedOption(optionId);
    
    if (currentStepContent?.type === 'quiz') {
      const selectedOption = currentStepContent.options?.find(option => option.id === optionId);
      setIsAnswerCorrect(selectedOption?.isCorrect || false);
    }
  };

  const handleNextStep = () => {
    if (currentStep < totalSteps) {
      setCurrentStep(currentStep + 1);
      resetStepState();
    } else if (content?.nextLessonId === "submit") {
      navigate(`/courses/${courseId}/lessons/${lessonId}/submit`);
    } else if (content?.nextLessonId === "finish") {
      navigate(`/courses/${courseId}/complete`);
    } else if (content?.nextLessonId) {
      navigate(`/courses/${courseId}/lessons/${content.nextLessonId}`);
    }
  };

  const handlePreviousStep = () => {
    if (currentStep > 1) {
      setCurrentStep(currentStep - 1);
      resetStepState();
    }
  };

  const resetStepState = () => {
    setSelectedOption(null);
    setIsAnswerCorrect(null);
    setTestPassed(false);
    setOutput('');
    
    const nextStep = content?.steps[currentStep];
    if (nextStep?.code) {
      setCode(nextStep.code);
    }
  };

  const evaluateCode = (code: string) => {
    try {
      // Extract print statements
      const printMatch = code.match(/fmt\.Println\((["'])(.*?)\1\)/);
      if (printMatch) {
        return printMatch[2];
      }
      
      const printfMatch = code.match(/fmt\.Printf\((["'])(.*?)\1.*\)/);
      if (printfMatch) {
        return printfMatch[2];
      }
      
      return 'Program executed successfully';
    } catch (error) {
      return `Error: ${error}`;
    }
  };

  if (!content || !currentStepContent) {
    return (
      <div className="min-h-screen bg-gray-900 flex items-center justify-center p-4">
        <div className="bg-gray-800 rounded-xl p-8 text-center max-w-lg">
          <div className="bg-gray-700 rounded-full h-24 w-24 flex items-center justify-center mx-auto mb-6">
            <Code className="h-12 w-12 text-blue-400" />
          </div>
          <h1 className="text-4xl font-bold text-white mb-4">Lesson Not Found</h1>
          <p className="text-lg text-gray-300 mb-8">
            Oops! We couldn't find the lesson you're looking for. It might have been moved or doesn't exist.
          </p>
          <button
            onClick={() => navigate('/courses')}
            className="inline-flex items-center px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition duration-200"
          >
            Browse Courses
          </button>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-900 flex flex-col">
      {/* Header */}
      <header className="bg-gray-800 border-b border-gray-700 py-2 px-4">
        <div className="flex items-center justify-between">
          <div className="flex items-center">
            <Logo />
            <div className="h-6 w-px bg-gray-700 mx-4" />
            <div>
              <h1 className="text-lg font-medium text-white">{content.title}</h1>
              <p className="text-sm text-gray-400">{content.description}</p>
            </div>
          </div>
          <div className="flex items-center space-x-4">
            <button
              onClick={() => navigate('/courses')}
              className="text-gray-300 hover:text-white transition-colors"
            >
              Exit Course
            </button>
          </div>
        </div>
      </header>

      <div className="flex flex-1 overflow-hidden">
        {/* Left Panel - Lesson Content */}
        <div className="w-1/2 bg-gray-800 overflow-auto">
          <div className="p-6">
            {/* Progress Bar */}
            <div className="mb-6">
              <div className="flex items-center justify-between text-gray-400 text-sm mb-2">
                <span>Step {currentStep} of {totalSteps}</span>
                <div className="flex space-x-1">
                  {Array.from({ length: totalSteps }).map((_, i) => (
                    <div
                      key={i}
                      className={`h-1 w-8 rounded-full ${
                        i < currentStep ? 'bg-blue-500' : 'bg-gray-700'
                      }`}
                    />
                  ))}
                </div>
              </div>
            </div>

            {/* Step Content */}
            <div className="mb-6">
              <h2 className="text-2xl font-bold text-white mb-4">
                {currentStepContent.title}
              </h2>
              
              <div 
                className="prose prose-invert max-w-none mb-8"
                dangerouslySetInnerHTML={{ __html: currentStepContent.content }}
              />
            </div>
          </div>
        </div>
        
        {/* Right Panel - Code Editor */}
        <div className="w-1/2 bg-gray-900 flex flex-col">
          {/* Editor Header */}
          <div className="flex items-center justify-between p-2 border-b border-gray-800">
            <div className="flex items-center">
              <span className="text-sm font-medium text-gray-400">main.go</span>
            </div>
            <div className="flex items-center space-x-2">
              <button
                onClick={handleRunCode}
                disabled={isRunning}
                className={`flex items-center px-3 py-1.5 rounded-md text-sm font-medium ${
                  isRunning 
                    ? 'bg-gray-700 text-gray-400 cursor-not-allowed'
                    : 'bg-green-600 text-white hover:bg-green-700'
                }`}
              >
                <Play className="h-4 w-4 mr-2" />
                {isRunning ? 'Running...' : 'Run'}
              </button>
              <button
                onClick={handleCopyCode}
                className="p-1.5 text-gray-400 hover:text-white"
                title="Copy Code"
              >
                <Copy className="h-4 w-4" />
              </button>
              <button
                onClick={handleDownloadCode}
                className="p-1.5 text-gray-400 hover:text-white"
                title="Download Code"
              >
                <Download className="h-4 w-4" />
              </button>
              <button
                onClick={handleClearCode}
                className="p-1.5 text-gray-400 hover:text-white"
                title="Clear Code"
              >
                <Trash className="h-4 w-4" />
              </button>
            </div>
          </div>
          
          {/* Code Editor */}
          <div className="flex-1 relative">
            <textarea
              value={code}
              onChange={(e) => setCode(e.target.value)}
              className="w-full h-full bg-gray-900 text-gray-300 font-mono p-4 resize-none focus:outline-none"
              spellCheck="false"
            />
          </div>
          
          {/* Console Output */}
          <div className={`border-t border-gray-800 ${consoleExpanded ? 'h-1/2' : 'h-40'}`}>
            <div className="flex items-center justify-between p-2 bg-gray-800">
              <div className="text-sm font-medium text-gray-400">Console</div>
              <button
                onClick={() => setConsoleExpanded(!consoleExpanded)}
                className="p-1 text-gray-400 hover:text-white"
              >
                <Maximize2 className="h-4 w-4" />
              </button>
            </div>
            <div className="p-4 font-mono text-sm text-gray-300 overflow-auto h-[calc(100%-36px)] bg-gray-900">
              {output || 'Run your code to see the output here...'}
            </div>
          </div>
        </div>
      </div>

      {/* Footer */}
      <footer className="bg-gray-800 border-t border-gray-700 py-3 px-4">
        <div className="flex items-center justify-between">
          <button
            onClick={handlePreviousStep}
            disabled={currentStep === 1}
            className={`flex items-center px-4 py-2 rounded-md ${
              currentStep === 1
                ? 'bg-gray-700 text-gray-400 cursor-not-allowed'
                : 'bg-gray-700 text-white hover:bg-gray-600'
            }`}
          >
            <ChevronLeft className="mr-2 h-5 w-5" />
            Previous Step
          </button>
          
          <button
            onClick={handleNextStep}
            className="flex items-center px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700"
          >
            {currentStep === totalSteps && content.nextLessonId === "submit" 
              ? 'Continue to Submission'
              : currentStep === totalSteps && content.nextLessonId === "finish" 
                ? 'Complete Lesson' 
                : 'Next Step'}
            <ChevronRight className="ml-2 h-5 w-5" />
          </button>
        </div>
      </footer>
    </div>
  );
};

export default LearningPage;

/== LoginPage.tsx
import React, { useState } from 'react';
import { useNavigate, useLocation } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import AuthLayout from '../components/templates/AuthLayout';
import AuthHeader from '../components/molecules/AuthHeader';
import AuthForm from '../components/molecules/AuthForm';

const LoginPage = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const navigate = useNavigate();
  const location = useLocation();
  const { login, isLoading, error } = useAuthStore();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      await login(email, password);
      const { error: authError, user } = useAuthStore.getState();
      if (!authError) {
        // Redirect based on user role
        if (user?.role === 'admin') {
          navigate('/cms');
        } else {
          // Get the previous path from state or default to '/'
          const from = location.state?.from?.pathname || '/';
          
          // Redirect based on origin
          if (from === '/pricing') {
            navigate('/payment');
          } else if (from === '/') {
            navigate('/dashboard');
          } else {
            navigate(from);
          }
        }
      }
    } catch (err) {
      console.error('Login error:', err);
    }
  };

  return (
    <AuthLayout>
      <AuthHeader
        title="Welcome Back!"
        subtitle="Sign in to continue your learning journey"
      />
      <AuthForm
        isLogin={true}
        onSubmit={handleSubmit}
        isLoading={isLoading}
        error={error || undefined}
        email={email}
        setEmail={setEmail}
        password={password}
        setPassword={setPassword}
      />
    </AuthLayout>
  );
};

export default LoginPage;

/== NotFoundPage.tsx
import Card from '../components/atoms/Card';

const NotFoundPage = () => {
  return (
    <div className="min-h-[calc(100vh-64px)] bg-gray-50 flex items-center justify-center px-4 py-12">
      <Card className="text-center p-8 max-w-lg">
        {/* ... card content */}
      </Card>
    </div>
  );
};

export default NotFoundPage;

/== PaymentFailedPage.tsx
import React from 'react';
import { useNavigate } from 'react-router-dom';
import { XCircle } from 'lucide-react';

const PaymentFailedPage = () => {
  const navigate = useNavigate();

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center p-4">
      <div className="max-w-md w-full bg-white rounded-xl shadow-lg p-8 text-center">
        <div className="w-16 h-16 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-6">
          <XCircle className="h-8 w-8 text-red-500" />
        </div>
        
        <h1 className="text-2xl font-bold text-gray-900 mb-2">
          Payment Failed
        </h1>
        
        <p className="text-gray-600 mb-8">
          We couldn't process your payment. Please try again or use a different payment method.
        </p>
        
        <div className="space-y-4">
          <button
            onClick={() => navigate(-1)}
            className="w-full py-3 px-4 bg-primary-500 text-white rounded-lg font-medium hover:bg-primary-600"
          >
            Try Again
          </button>
          
          <button
            onClick={() => navigate('/support')}
            className="w-full py-3 px-4 border border-gray-300 text-gray-700 rounded-lg font-medium hover:bg-gray-50"
          >
            Contact Support
          </button>
        </div>
      </div>
    </div>
  );
};

export default PaymentFailedPage;

/== PaymentFailedPageBK.tsx
import React from 'react';
import { useNavigate } from 'react-router-dom';
import { XCircle } from 'lucide-react';

const PaymentFailedPage = () => {
  const navigate = useNavigate();

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center p-4">
      <div className="max-w-md w-full bg-white rounded-xl shadow-lg p-8 text-center">
        <div className="w-16 h-16 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-6">
          <XCircle className="h-8 w-8 text-red-500" />
        </div>
        
        <h1 className="text-2xl font-bold text-gray-900 mb-2">
          Payment Failed
        </h1>
        
        <p className="text-gray-600 mb-8">
          We couldn't process your payment. Please try again or use a different payment method.
        </p>
        
        <div className="space-y-4">
          <button
            onClick={() => navigate(-1)}
            className="w-full py-3 px-4 bg-primary-500 text-white rounded-lg font-medium hover:bg-primary-600"
          >
            Try Again
          </button>
          
          <button
            onClick={() => navigate('/support')}
            className="w-full py-3 px-4 border border-gray-300 text-gray-700 rounded-lg font-medium hover:bg-gray-50"
          >
            Contact Support
          </button>
        </div>
      </div>
    </div>
  );
};

export default PaymentFailedPage;

/== PaymentPage.tsx
import React, { useState, useEffect, useRef } from 'react';
import { useLocation, useNavigate } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import { X } from 'lucide-react';
import PaymentSummary from '../components/molecules/PaymentSummary';
import PaymentForm from '../components/molecules/PaymentForm';

// Define payment method type
type PaymentMethodType = 'card' | 'paypal' | 'external';

const PaymentPage = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { user } = useAuthStore();
  const [paymentMethod, setPaymentMethod] = useState<PaymentMethodType>('card');
  const [cardNumber, setCardNumber] = useState('');
  const [cardName, setCardName] = useState('');
  const [expiry, setExpiry] = useState('');
  const [cvv, setCvv] = useState('');
  const [isProcessing, setIsProcessing] = useState(false);
  const [showExternalPayment, setShowExternalPayment] = useState(false);
  const iframeRef = useRef<HTMLIFrameElement>(null);
  
  // Extract plan and billing cycle from location state
  const { plan, billingCycle } = location.state || { plan: 'premium', billingCycle: 'yearly' };

  // Function to monitor iframe URL changes
  const setupIframeURLChangeDetection = () => {
    const iframe = iframeRef.current;
    if (!iframe) return;

    // Function to detect URL changes in iframe
    function iframeURLChange(iframe: HTMLIFrameElement, callback: (url: string) => void) {
      const unloadHandler = function() {
        // Timeout needed because the URL changes immediately after
        // the `unload` event is dispatched.
        setTimeout(function() {
          try {
            // This will throw a CORS error if iframe is on different domain
            // But we're catching the error, so it's okay
            const newUrl = iframe.contentWindow?.location.href;
            if (newUrl) {
              callback(newUrl);
            }
          } catch (e) {
            console.log("Cannot access iframe URL due to same-origin policy");
          }
        }, 0);
      };

      function attachUnload() {
        try {
          // This will throw if iframe is cross-origin
          if (iframe.contentWindow) {
            // Remove the unloadHandler in case it was already attached.
            // Otherwise, the change will be dispatched twice.
            iframe.contentWindow.removeEventListener("unload", unloadHandler);
            iframe.contentWindow.addEventListener("unload", unloadHandler);
          }
        } catch (e) {
          console.log("Cannot attach unload handler due to same-origin policy");
          // Fallback: check URL periodically
          setInterval(() => {
            try {
              const newUrl = iframe.contentWindow?.location.href;
              if (newUrl) {
                callback(newUrl);
              }
            } catch (e) {
              // Silently ignore CORS errors
            }
          }, 500);
        }
      }

      // Attach to load event
      iframe.addEventListener("load", attachUnload);
      
      // Try to attach immediately as well
      attachUnload();
    }

    // Use the iframeURLChange function
    if (iframe) {
      console.log("Setting up iframe URL change detection");
      iframeURLChange(iframe, (newURL) => {
        console.log("URL changed:", newURL);
        // Check if it's the success URL
        if (newURL.includes('goakal.com/payment/success')) {
          try {
            // Parse the URL to get parameters
            const url = new URL(newURL);
            const token = url.searchParams.get('token');
            const school = url.searchParams.get('school');
            
            if (token && school) {
              console.log(`Payment successful! Token: ${token}, School: ${school}`);
              
              // Save to localStorage as fallback
              localStorage.setItem('payment_token', token);
              localStorage.setItem('payment_school', school);
              localStorage.setItem('payment_status', 'success');
              
              // Redirect to success page
              navigate('/payment/success', { 
                state: { 
                  plan,
                  billingCycle,
                  amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle as 'yearly' | 'monthly'].final,
                  token,
                  school
                }
              });
            }
          } catch (error) {
            console.error('Error processing success URL:', error);
          }
        }
      });
    }
  };

  // Set up iframe URL change detection when external payment is shown
  useEffect(() => {
    if (showExternalPayment) {
      // Allow a bit of time for the iframe to load
      setTimeout(setupIframeURLChangeDetection, 1000);
    }
  }, [showExternalPayment]);

  // Check for URL parameters on component mount
  useEffect(() => {
    const params = new URLSearchParams(location.search);
    const token = params.get('token');
    const school = params.get('school');
    
    // If we have token and school in URL, go to success page
    if (token && school) {
      navigate('/payment/success', { 
        state: { 
          plan,
          billingCycle,
          amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle as 'yearly' | 'monthly'].final,
          token,
          school
        }
      });
    }
  }, [location, navigate, plan, billingCycle]);

  // Pricing configuration
  const prices = {
    premium: {
      yearly: {
        original: 1735125,
        discount: 1156807,
        final: 578317
      },
      monthly: {
        original: 162463,
        discount: 0,
        final: 162463
      }
    },
    lifetime: {
      final: 1923097
    }
  };

  // Function to handle URL paste from clipboard
  const handleUrlPaste = (e: React.ClipboardEvent<HTMLInputElement>) => {
    const pastedText = e.clipboardData.getData('text');
    if (pastedText.includes('https://lynk.id/payment-thankyou')) {
      try {
        const url = new URL(pastedText);
        const token = url.searchParams.get('token');
        const school = url.searchParams.get('school');
        
        if (token && school) {
          // Navigate to success page with the extracted data
          navigate('/payment/success', { 
            state: { 
              plan,
              billingCycle,
              amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle as 'yearly' | 'monthly'].final,
              token,
              school
            }
          });
        }
      } catch (error) {
        console.error('Error parsing pasted URL:', error);
      }
    }
  };

  const handlePayment = async (e: React.FormEvent) => {
    e.preventDefault();
    
    if (paymentMethod === 'external') {
      // Show external payment iframe
      setShowExternalPayment(true);
      return;
    }
    
    setIsProcessing(true);

    try {
      // Simulate payment processing for card/PayPal
      await new Promise(resolve => setTimeout(resolve, 2000));
      
      // Randomly succeed or fail for demo purposes
      const success = Math.random() > 0.5;
      
      if (success) {
        navigate('/payment/success', { 
          state: { 
            plan,
            billingCycle,
            amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle as 'yearly' | 'monthly'].final
          }
        });
      } else {
        navigate('/payment/failed');
      }
    } catch (error) {
      navigate('/payment/failed');
    } finally {
      setIsProcessing(false);
    }
  };

  if (!user) {
    navigate('/login');
    return null;
  }

  const currentPrice = plan === 'lifetime' 
    ? { original: prices.lifetime.final, discount: 0, final: prices.lifetime.final }
    : prices.premium[billingCycle as 'yearly' | 'monthly'];

  // Render external payment iframe if in external payment mode
  if (showExternalPayment) {
    return (
      <div className="min-h-screen bg-gray-900 flex flex-col p-6">
        <div className="flex items-center justify-between mb-6">
          <h2 className="text-xl font-bold text-white">External Payment Processing</h2>
          <button 
            onClick={() => setShowExternalPayment(false)}
            className="p-2 rounded-full bg-gray-700 hover:bg-gray-600 text-white"
          >
            <X className="h-6 w-6" />
          </button>
        </div>
        
        <div className="flex-grow bg-white rounded-xl shadow-lg p-6 flex flex-col">
          {/* Top part with iframe and monitoring */}
          <div className="flex-grow flex flex-col">
            <div className="flex-grow border border-gray-200 rounded-lg overflow-hidden">
              <iframe
                ref={iframeRef}
                src="https://lynk.id/codergrowth/8zqzq0lzw699/checkout"
                title="External Payment Form"
                className="w-full h-full border-0"
                id="payment-iframe"
              />
            </div>
            
            {/* Manual URL entry fallback */}
            <div className="mt-4 p-4 bg-gray-50 rounded-lg">
              <h3 className="font-medium text-gray-800 mb-2">If Automatic Detection Fails:</h3>
              <p className="text-gray-600 text-sm mb-3">
                After completing payment, if you're not automatically redirected, paste the success URL here:
              </p>
              <input
                type="text"
                className="w-full p-2 border border-gray-300 rounded"
                placeholder="https://lynk.id/payment-thankyou?sess=cmVmPTAzZTlkZDA1MjI5NzYwOTExMGI5MTc5ODQ0MGQ0ODgyJmJpbGxlZF9hbW91bnQ9SURSIDAmcmV0dXJuX3VyaT1odHRwOi8vbHluay5pZC9jb2Rlcmdyb3d0aC84enF6cTBsenc2OTkvY2hlY2tvdXQvY29kZXJncm93dGg="
                onPaste={handleUrlPaste}
              />
            </div>
          </div>
          
          {/* Payment details at bottom */}
          <div className="mt-4 p-4 bg-blue-50 border border-blue-100 rounded-lg">
            <div className="flex justify-between items-center text-blue-800">
              <span className="font-medium">Payment Amount:</span>
              <span className="font-bold">Rp {currentPrice.final.toLocaleString()}</span>
            </div>
            <div className="mt-2 text-xs text-blue-600">
              <p>Your payment is being processed securely. This page will automatically detect when payment is complete.</p>
            </div>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-900 flex">
      {/* Left Panel - Order Summary */}
      <div className="w-1/2 bg-gray-900 p-12">
        <div className="max-w-md mx-auto">
          <button onClick={() => navigate(-1)} className="text-white mb-8">
            <X className="h-6 w-6" />
          </button>
          
          <h1 className="text-2xl font-bold text-white mb-2">
            Enjoy unlimited access to our courses with Coder Growth PRO.
          </h1>
          
          <PaymentSummary
            originalPrice={currentPrice.original}
            discount={currentPrice.discount}
            finalPrice={currentPrice.final}
            plan={plan}
            billingCycle={billingCycle}
          />
        </div>
      </div>

      {/* Right Panel - Payment Form */}
      <div className="w-1/2 bg-white p-12">
        <div className="max-w-md mx-auto">
          <h2 className="text-2xl font-bold text-gray-900 mb-8">Payment Details</h2>
          
          {/* Payment method selector */}
          <div className="mb-6">
            <div className="flex space-x-4">
              <button
                type="button"
                onClick={() => setPaymentMethod('card')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'card' 
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                Credit Card
              </button>
              <button
                type="button"
                onClick={() => setPaymentMethod('paypal')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'paypal'
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                PayPal
              </button>
              <button
                type="button"
                onClick={() => setPaymentMethod('external')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'external'
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                External
              </button>
            </div>
          </div>
          
          {(paymentMethod === 'card' || paymentMethod === 'paypal') && (
            <PaymentForm
              paymentMethod={paymentMethod}
              setPaymentMethod={setPaymentMethod}
              cardNumber={cardNumber}
              setCardNumber={setCardNumber}
              cardName={cardName}
              setCardName={setCardName}
              expiry={expiry}
              setExpiry={setExpiry}
              cvv={cvv}
              setCvv={setCvv}
              onSubmit={handlePayment}
              isProcessing={isProcessing}
            />
          )}
          
          {paymentMethod === 'external' && (
            <div className="mt-6">
              <div className="mb-6 p-4 bg-gray-50 rounded-lg">
                <h3 className="font-medium text-gray-900 mb-2">About External Payment:</h3>
                <p className="text-gray-600 mb-4">
                  You'll be redirected to our external payment processor to complete your purchase.
                  The process is secure and this page will automatically update once payment is complete.
                </p>
              </div>
              
              <button
                onClick={handlePayment}
                className="w-full bg-primary-500 hover:bg-primary-600 text-white py-3 px-4 rounded-md font-medium"
                disabled={isProcessing}
              >
                {isProcessing ? 'Processing...' : 'Continue to External Payment'}
              </button>
            </div>
          )}
          
          <p className="mt-8 text-sm text-gray-500 text-center">
            By continuing, you agree to our Terms of Service and authorize this recurring charge.
          </p>
        </div>
      </div>
    </div>
  );
};

export default PaymentPage;

/== PaymentPageBK.tsx
import React, { useState } from 'react';
import { useLocation, useNavigate } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import { X } from 'lucide-react';
import PaymentSummary from '../components/molecules/PaymentSummary';
import PaymentForm from '../components/molecules/PaymentForm';

const PaymentPage = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { user } = useAuthStore();
  const [paymentMethod, setPaymentMethod] = useState<'card' | 'paypal'>('card');
  const [cardNumber, setCardNumber] = useState('');
  const [cardName, setCardName] = useState('');
  const [expiry, setExpiry] = useState('');
  const [cvv, setCvv] = useState('');
  const [isProcessing, setIsProcessing] = useState(false);

  const { plan, billingCycle } = location.state || { plan: 'premium', billingCycle: 'yearly' };

  const prices = {
    premium: {
      yearly: {
        original: 1735125,
        discount: 1156807,
        final: 578317
      },
      monthly: {
        original: 162463,
        discount: 0,
        final: 162463
      }
    },
    lifetime: {
      final: 1923097
    }
  };

  const handlePayment = async (e: React.FormEvent) => {
    e.preventDefault();
    setIsProcessing(true);

    try {
      // Simulate payment processing
      await new Promise(resolve => setTimeout(resolve, 2000));
      
      // Randomly succeed or fail for demo purposes
      const success = Math.random() > 0.5;
      
      if (success) {
        navigate('/payment/success', { 
          state: { 
            plan,
            billingCycle,
            amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle].final
          }
        });
      } else {
        navigate('/payment/failed');
      }
    } catch (error) {
      navigate('/payment/failed');
    }
  };

  if (!user) {
    navigate('/login');
    return null;
  }

  const currentPrice = plan === 'lifetime' 
    ? { original: prices.lifetime.final, discount: 0, final: prices.lifetime.final }
    : prices.premium[billingCycle];

  return (
    <div className="min-h-screen bg-gray-900 flex">
      {/* Left Panel - Order Summary */}
      <div className="w-1/2 bg-gray-900 p-12">
        <div className="max-w-md mx-auto">
          <button onClick={() => navigate(-1)} className="text-white mb-8">
            <X className="h-6 w-6" />
          </button>
          
          <h1 className="text-2xl font-bold text-white mb-2">
            Enjoy unlimited access to our courses with Coder Growth PRO.
          </h1>
          
          <PaymentSummary
            originalPrice={currentPrice.original}
            discount={currentPrice.discount}
            finalPrice={currentPrice.final}
            plan={plan}
            billingCycle={billingCycle}
          />
        </div>
      </div>

      {/* Right Panel - Payment Form */}
      <div className="w-1/2 bg-white p-12">
        <div className="max-w-md mx-auto">
          <h2 className="text-2xl font-bold text-gray-900 mb-8">Payment Details</h2>
          
          <PaymentForm
            paymentMethod={paymentMethod}
            setPaymentMethod={setPaymentMethod}
            cardNumber={cardNumber}
            setCardNumber={setCardNumber}
            cardName={cardName}
            setCardName={setCardName}
            expiry={expiry}
            setExpiry={setExpiry}
            cvv={cvv}
            setCvv={setCvv}
            onSubmit={handlePayment}
            isProcessing={isProcessing}
          />
          
          <p className="mt-8 text-sm text-gray-500 text-center">
            By continuing, you agree to our Terms of Service and authorize this recurring charge.
          </p>
        </div>
      </div>
    </div>
  );
};

export default PaymentPage;

/== PaymentPageVer2.tsx
import React, { useState, useEffect, useRef } from 'react';
import { useLocation, useNavigate } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import { X } from 'lucide-react';
import PaymentSummary from '../components/molecules/PaymentSummary';
import PaymentForm from '../components/molecules/PaymentForm';

// Define payment method type
type PaymentMethodType = 'card' | 'paypal' | 'external';

// Backend proxy server URL - Pastikan URL ini sesuai dengan server Puppeteer Anda
const PROXY_SERVER_URL = 'http://localhost:3001/api';

const PaymentPage = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { user } = useAuthStore();
  const [paymentMethod, setPaymentMethod] = useState<PaymentMethodType>('card');
  const [cardNumber, setCardNumber] = useState('');
  const [cardName, setCardName] = useState('');
  const [expiry, setExpiry] = useState('');
  const [cvv, setCvv] = useState('');
  const [isProcessing, setIsProcessing] = useState(false);
  const [showExternalPayment, setShowExternalPayment] = useState(false);
  const [activeSessionId, setActiveSessionId] = useState<string | null>(null);
  const paymentWindowRef = useRef<Window | null>(null);
  const pollIntervalRef = useRef<number | null>(null);
  const [paymentStatus, setPaymentStatus] = useState<string>('pending');
  
  // Extract plan and billing cycle from location state
  const { plan, billingCycle } = location.state || { plan: 'premium', billingCycle: 'yearly' };

  // Check URL for session ID
  useEffect(() => {
    const params = new URLSearchParams(location.search);
    const sessionIdParam = params.get('session');
    
    if (sessionIdParam) {
      setActiveSessionId(sessionIdParam);
      startPaymentStatusPolling(sessionIdParam);
    }
  }, [location]);

  // Start polling for payment status
  const startPaymentStatusPolling = (paymentSessionId: string) => {
    // Clear any existing interval
    if (pollIntervalRef.current) {
      window.clearInterval(pollIntervalRef.current);
    }
    
    console.log(`Starting polling for session: ${paymentSessionId}`);
    
    // Set up polling interval (every 2 seconds)
    pollIntervalRef.current = window.setInterval(() => {
      checkPaymentStatus(paymentSessionId);
    }, 2000);
  };

  // Check payment status with backend
  const checkPaymentStatus = async (paymentSessionId: string) => {
    try {
      console.log(`Checking payment status for: ${paymentSessionId}`);
      const response = await fetch(`${PROXY_SERVER_URL}/payment/check-status/${paymentSessionId}`);
      
      if (!response.ok) {
        throw new Error(`Server responded with status: ${response.status}`);
      }
      
      const data = await response.json();
      console.log('Payment status response:', data);
      
      // Update status
      setPaymentStatus(data.status || 'pending');
      
      if (data.status === 'success') {
        console.log('Payment successful! Redirecting to success page...');
        
        // Clear polling interval
        if (pollIntervalRef.current) {
          window.clearInterval(pollIntervalRef.current);
          pollIntervalRef.current = null;
        }
        
        // Calculate the final amount
        const currentPrice = data.plan === 'lifetime' 
          ? { final: prices.lifetime.final }
          : prices.premium[data.billingCycle as 'yearly' | 'monthly'];
        
        // Redirect to success page
        navigate('/payment/success', { 
          state: { 
            plan: data.plan,
            billingCycle: data.billingCycle,
            amount: currentPrice.final,
            token: data.token,
            school: data.school
          }
        });
      } else if (data.status === 'error' || data.status === 'timeout') {
        console.error(`Payment failed with status: ${data.status}`);
        
        // Clear polling interval
        if (pollIntervalRef.current) {
          window.clearInterval(pollIntervalRef.current);
          pollIntervalRef.current = null;
        }
        
        alert('Payment process encountered an error. Please try again.');
        setShowExternalPayment(false);
      }
    } catch (error) {
      console.error('Error checking payment status:', error);
    }
  };

  // Clean up intervals on unmount
  useEffect(() => {
    return () => {
      if (pollIntervalRef.current) {
        window.clearInterval(pollIntervalRef.current);
      }
    };
  }, []);

  // Pricing configuration
  const prices = {
    premium: {
      yearly: {
        original: 1735125,
        discount: 1156807,
        final: 578317
      },
      monthly: {
        original: 162463,
        discount: 0,
        final: 162463
      }
    },
    lifetime: {
      final: 1923097
    }
  };

  // Create payment session and redirect to payment gateway
  const initiateExternalPayment = async () => {
    setIsProcessing(true);
    
    try {
      console.log('Initiating external payment...');
      
      // Create payment session with the backend
      const response = await fetch(`${PROXY_SERVER_URL}/payment/create-session`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          plan,
          billingCycle,
          userId: user?.id
        }),
      });
      
      if (!response.ok) {
        throw new Error(`Server responded with status: ${response.status}`);
      }
      
      const data = await response.json();
      console.log('Payment session created:', data);
      
      if (data.sessionId && data.redirectUrl) {
        // Store session ID
        setActiveSessionId(data.sessionId);
        
        // Open payment window
        const paymentWindow = window.open(data.redirectUrl, '_blank');
        paymentWindowRef.current = paymentWindow;
        
        // Set external payment mode
        setShowExternalPayment(true);
        
        // Start polling for payment status
        startPaymentStatusPolling(data.sessionId);
      } else {
        throw new Error('Failed to create payment session');
      }
    } catch (error) {
      console.error('Error initiating external payment:', error);
      alert('Failed to initiate external payment. Please try again.');
    } finally {
      setIsProcessing(false);
    }
  };

  const handlePayment = async (e: React.FormEvent) => {
    e.preventDefault();
    
    if (paymentMethod === 'external') {
      // Initiate external payment
      await initiateExternalPayment();
      return;
    }
    
    setIsProcessing(true);

    try {
      // Simulate payment processing for card/PayPal
      await new Promise(resolve => setTimeout(resolve, 2000));
      
      // Randomly succeed or fail for demo purposes
      const success = Math.random() > 0.5;
      
      if (success) {
        navigate('/payment/success', { 
          state: { 
            plan,
            billingCycle,
            amount: plan === 'lifetime' ? prices.lifetime.final : prices.premium[billingCycle as 'yearly' | 'monthly'].final
          }
        });
      } else {
        navigate('/payment/failed');
      }
    } catch (error) {
      console.error('Error processing payment:', error);
      navigate('/payment/failed');
    } finally {
      setIsProcessing(false);
    }
  };

  if (!user) {
    navigate('/login');
    return null;
  }

  const currentPrice = plan === 'lifetime' 
    ? { original: prices.lifetime.final, discount: 0, final: prices.lifetime.final }
    : prices.premium[billingCycle as 'yearly' | 'monthly'];

  // Render external payment status panel if in external payment mode
  if (showExternalPayment) {
    return (
      <div className="min-h-screen bg-gray-900 flex flex-col p-6">
        <div className="flex items-center justify-between mb-6">
          <h2 className="text-xl font-bold text-white">External Payment in Progress</h2>
          <button 
            onClick={() => {
              // Clear polling interval
              if (pollIntervalRef.current) {
                window.clearInterval(pollIntervalRef.current);
                pollIntervalRef.current = null;
              }
              setShowExternalPayment(false);
            }}
            className="p-2 rounded-full bg-gray-700 hover:bg-gray-600 text-white"
          >
            <X className="h-6 w-6" />
          </button>
        </div>
        
        <div className="flex-grow bg-white rounded-xl shadow-lg p-6 flex flex-col">
          <div className="flex items-center justify-center flex-col flex-grow">
            <div className="w-20 h-20 border-4 border-primary-500 border-t-transparent rounded-full animate-spin mb-6"></div>
            
            <h3 className="text-xl font-medium text-gray-900 mb-2">
              {paymentStatus === 'pending' 
                ? 'Processing Your Payment' 
                : paymentStatus === 'success' 
                  ? 'Payment Successful!' 
                  : 'Payment Processing...'}
            </h3>
            
            <p className="text-gray-600 text-center max-w-md mb-6">
              {paymentStatus === 'pending' 
                ? 'Please complete your payment in the newly opened tab. This page will automatically update once your payment is processed.' 
                : paymentStatus === 'success' 
                  ? 'Your payment has been successfully processed. You will be redirected shortly.' 
                  : 'We are waiting for confirmation of your payment. Please complete the payment in the opened tab.'}
            </p>
            
            <div className="w-full max-w-md bg-primary-50 border border-primary-200 rounded-lg p-4 mb-6">
              <h4 className="font-medium text-primary-800 mb-2">Payment Details:</h4>
              <div className="flex justify-between text-primary-700 mb-1">
                <span>Plan:</span>
                <span className="font-medium">{plan === 'lifetime' ? 'Lifetime Access' : 'Premium'}</span>
              </div>
              <div className="flex justify-between text-primary-700 mb-1">
                <span>Billing:</span>
                <span className="font-medium">{plan === 'lifetime' ? 'One-time' : (billingCycle === 'yearly' ? 'Annual' : 'Monthly')}</span>
              </div>
              <div className="flex justify-between text-primary-700 mt-2 pt-2 border-t border-primary-200">
                <span>Total:</span>
                <span className="font-medium">Rp {currentPrice.final.toLocaleString()}</span>
              </div>
              {activeSessionId && (
                <div className="mt-2 pt-2 border-t border-primary-200 text-xs text-primary-600">
                  <span>Session ID: {activeSessionId.substring(0, 8)}...</span>
                  <div className="mt-1">Status: <span className="font-semibold capitalize">{paymentStatus}</span></div>
                </div>
              )}
            </div>
            
            <button
              onClick={() => {
                // Re-focus payment window if it exists
                if (paymentWindowRef.current && !paymentWindowRef.current.closed) {
                  paymentWindowRef.current.focus();
                } else if (activeSessionId) {
                  // Restart payment if window was closed but we have a session
                  const paymentWindow = window.open(`https://goakal.com/haryadi/setup-home-server/ec0sb/apply`, '_blank');
                  paymentWindowRef.current = paymentWindow;
                } else {
                  // Start a new payment if no active session
                  initiateExternalPayment();
                }
              }}
              className="bg-primary-500 hover:bg-primary-600 text-white py-3 px-6 rounded-lg font-medium"
            >
              Return to Payment Window
            </button>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-900 flex">
      {/* Left Panel - Order Summary */}
      <div className="w-1/2 bg-gray-900 p-12">
        <div className="max-w-md mx-auto">
          <button onClick={() => navigate(-1)} className="text-white mb-8">
            <X className="h-6 w-6" />
          </button>
          
          <h1 className="text-2xl font-bold text-white mb-2">
            Enjoy unlimited access to our courses with Coder Growth PRO.
          </h1>
          
          <PaymentSummary
            originalPrice={currentPrice.original}
            discount={currentPrice.discount}
            finalPrice={currentPrice.final}
            plan={plan}
            billingCycle={billingCycle}
          />
        </div>
      </div>

      {/* Right Panel - Payment Form */}
      <div className="w-1/2 bg-white p-12">
        <div className="max-w-md mx-auto">
          <h2 className="text-2xl font-bold text-gray-900 mb-8">Payment Details</h2>
          
          {/* Payment method selector */}
          <div className="mb-6">
            <div className="flex space-x-4">
              <button
                type="button"
                onClick={() => setPaymentMethod('card')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'card' 
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                Credit Card
              </button>
              <button
                type="button"
                onClick={() => setPaymentMethod('paypal')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'paypal'
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                PayPal
              </button>
              <button
                type="button"
                onClick={() => setPaymentMethod('external')}
                className={`flex-1 py-2 px-4 rounded-md border ${
                  paymentMethod === 'external'
                    ? 'border-primary-500 bg-primary-50 text-primary-700' 
                    : 'border-gray-300 text-gray-700'
                }`}
              >
                External
              </button>
            </div>
          </div>
          
          {(paymentMethod === 'card' || paymentMethod === 'paypal') && (
            <PaymentForm
              paymentMethod={paymentMethod}
              setPaymentMethod={setPaymentMethod}
              cardNumber={cardNumber}
              setCardNumber={setCardNumber}
              cardName={cardName}
              setCardName={setCardName}
              expiry={expiry}
              setExpiry={setExpiry}
              cvv={cvv}
              setCvv={setCvv}
              onSubmit={handlePayment}
              isProcessing={isProcessing}
            />
          )}
          
          {paymentMethod === 'external' && (
            <div className="mt-6">
              <div className="mb-6 p-4 bg-gray-50 rounded-lg">
                <h3 className="font-medium text-gray-900 mb-2">About External Payment:</h3>
                <p className="text-gray-600 mb-4">
                  You'll be redirected to our external payment processor to complete your purchase.
                  The process is secure and this page will automatically update once payment is complete.
                </p>
                <div className="text-xs text-gray-500 p-2 bg-blue-50 rounded border border-blue-100">
                  <p className="mb-1 font-medium text-blue-700">How it works:</p>
                  <p>Our system will monitor the payment process and automatically detect when your payment is successful.</p>
                </div>
              </div>
              
              <button
                onClick={handlePayment}
                className="w-full bg-primary-500 hover:bg-primary-600 text-white py-3 px-4 rounded-md font-medium"
                disabled={isProcessing}
              >
                {isProcessing ? 'Processing...' : 'Continue to External Payment'}
              </button>
            </div>
          )}
          
          <p className="mt-8 text-sm text-gray-500 text-center">
            By continuing, you agree to our Terms of Service and authorize this recurring charge.
          </p>
        </div>
      </div>
    </div>
  );
};

export default PaymentPage;

/== PaymentSuccessPage.tsx
import React, { useEffect } from 'react';
import { useLocation, useNavigate } from 'react-router-dom';
import { CheckCircle } from 'lucide-react';

const PaymentSuccessPage = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { plan, billingCycle, amount, token, school } = location.state || {};

  // Clean up localStorage when component mounts
  useEffect(() => {
    localStorage.removeItem('payment_token');
    localStorage.removeItem('payment_school');
    localStorage.removeItem('payment_status');
    localStorage.removeItem('payment_plan');
    localStorage.removeItem('payment_billing_cycle');
    
    // If no state is available, try to redirect back to payment page
    if (!location.state) {
      navigate('/payment');
    }
  }, [location, navigate]);

  // Calculate the display values, handling potential undefined values
  const displayAmount = amount || 0;
  const displayPlan = plan || 'premium';
  const displayBillingCycle = billingCycle || 'yearly';

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center p-4">
      <div className="max-w-md w-full bg-white rounded-xl shadow-lg p-8 text-center">
        <div className="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-6">
          <CheckCircle className="h-8 w-8 text-green-500" />
        </div>
        
        <h1 className="text-2xl font-bold text-gray-900 mb-2">
          Payment Successful!
        </h1>
        
        <p className="text-gray-600 mb-6">
          Thank you for subscribing to Coder Growth PRO. Your payment has been processed successfully.
        </p>
        
        <div className="bg-gray-50 rounded-lg p-4 mb-8">
          <div className="text-sm text-gray-600 mb-2">Payment Summary</div>
          <div className="text-lg font-medium text-gray-900">
            Rp {displayAmount.toLocaleString()}
          </div>
          <div className="text-sm text-gray-500">
            {displayPlan} Plan - {displayBillingCycle === 'yearly' ? 'Annual' : 'Monthly'} Billing
          </div>
          
          {token && (
            <div className="mt-2 pt-2 border-t border-gray-200 text-xs text-gray-500">
              <div>Reference: {token.substring(0, 8)}...</div>
              {school && <div>School: {school}</div>}
            </div>
          )}
        </div>
        
        <div className="space-y-4">
          <button
            onClick={() => navigate('/billing')}
            className="w-full py-3 px-4 bg-primary-500 text-white rounded-lg font-medium hover:bg-primary-600"
          >
            View Billing History
          </button>
          
          <button
            onClick={() => navigate('/dashboard')}
            className="w-full py-3 px-4 border border-gray-300 text-gray-700 rounded-lg font-medium hover:bg-gray-50"
          >
            Go to Dashboard
          </button>
        </div>
      </div>
    </div>
  );
};

export default PaymentSuccessPage;

/== PaymentSuccessPageBK.tsx
import React from 'react';
import { useLocation, useNavigate } from 'react-router-dom';
import { CheckCircle } from 'lucide-react';

const PaymentSuccessPage = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { plan, billingCycle, amount } = location.state || {};

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center p-4">
      <div className="max-w-md w-full bg-white rounded-xl shadow-lg p-8 text-center">
        <div className="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-6">
          <CheckCircle className="h-8 w-8 text-green-500" />
        </div>
        
        <h1 className="text-2xl font-bold text-gray-900 mb-2">
          Payment Successful!
        </h1>
        
        <p className="text-gray-600 mb-6">
          Thank you for subscribing to Coder Growth PRO. Your payment has been processed successfully.
        </p>
        
        <div className="bg-gray-50 rounded-lg p-4 mb-8">
          <div className="text-sm text-gray-600 mb-2">Payment Summary</div>
          <div className="text-lg font-medium text-gray-900">
            Rp {amount?.toLocaleString() || '0'}
          </div>
          <div className="text-sm text-gray-500">
            {plan} Plan - {billingCycle === 'yearly' ? 'Annual' : 'Monthly'} Billing
          </div>
        </div>
        
        <div className="space-y-4">
          <button
            onClick={() => navigate('/billing')}
            className="w-full py-3 px-4 bg-primary-500 text-white rounded-lg font-medium hover:bg-primary-600"
          >
            View Billing History
          </button>
          
          <button
            onClick={() => navigate('/dashboard')}
            className="w-full py-3 px-4 border border-gray-300 text-gray-700 rounded-lg font-medium hover:bg-gray-50"
          >
            Go to Dashboard
          </button>
        </div>
      </div>
    </div>
  );
};

export default PaymentSuccessPage;

/== PricingPage.tsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import PricingSection from '../components/organisms/PricingSection';

const PricingPage = () => {
  const [billingCycle, setBillingCycle] = useState<'yearly' | 'monthly'>('yearly');
  const navigate = useNavigate();
  const { isAuthenticated } = useAuthStore();

  const handleSubscribe = (plan: string) => {
    if (!isAuthenticated) {
      navigate('/login?redirect=/payment');
      return;
    }
    navigate('/payment', { state: { plan, billingCycle } });
  };

  return (
    <div className="min-h-screen bg-gray-50">
      <PricingSection
        billingCycle={billingCycle}
        onBillingCycleChange={setBillingCycle}
        onSubscribe={handleSubscribe}
      />
    </div>
  );
};

export default PricingPage;

/== RegisterPage.tsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuthStore } from '../store/authStore';
import AuthLayout from '../components/templates/AuthLayout';
import AuthHeader from '../components/molecules/AuthHeader';
import AuthForm from '../components/molecules/AuthForm';

const RegisterPage = () => {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const navigate = useNavigate();
  const { register, isLoading, error } = useAuthStore();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      await register(name, email, password);
      const { error: authError } = useAuthStore.getState();
      if (!authError) {
        navigate('/dashboard');
      }
    } catch (err) {
      console.error('Registration error:', err);
    }
  };

  return (
    <AuthLayout>
      <AuthHeader
        title="Create Account"
        subtitle="Join our community of learners"
      />
      <AuthForm
        isLogin={false}
        onSubmit={handleSubmit}
        isLoading={isLoading}
        error={error || undefined}
        email={email}
        setEmail={setEmail}
        password={password}
        setPassword={setPassword}
        name={name}
        setName={setName}
      />
    </AuthLayout>
  );
};

export default RegisterPage;

/== RoadmapPage.tsx
import React, { useEffect, useMemo, useState } from 'react';
import { useParams } from 'react-router-dom';
import { Book, Code, Database, Server } from 'lucide-react';
import { useRoadmapStore } from '../store/roadmapStore';
import RoadmapFlow from '../components/organisms/RoadmapFlow';
import RoadmapModal from '../components/molecules/RoadmapModal';
import { transformToFlow } from '../utils/roadmapTransform';
import backendRoadmap from '../data/roadmaps/backend';
import Header from '../components/organisms/Header';

const RoadmapPage: React.FC = () => {
  const { name } = useParams<{ name: string }>();
  const { setCurrentRoadmap, setActiveNode, completedNodes, activeNode, completeNode } = useRoadmapStore();
  const [selectedNode, setSelectedNode] = useState<any>(null);
  const [isModalOpen, setIsModalOpen] = useState(false);

  useEffect(() => {
    if (name) {
      setCurrentRoadmap(name);
    }
  }, [name]);

  const { nodes, edges } = useMemo(
    () => transformToFlow(backendRoadmap, completedNodes, activeNode, { x: 50, y: 50 }),
    [backendRoadmap, completedNodes, activeNode]
  );

  const handleNodeClick = (nodeId: string) => {
    setActiveNode(nodeId);
    const node = backendRoadmap.find(n => n.id === nodeId) || 
                backendRoadmap.flatMap(n => n.children || []).find(n => n?.id === nodeId) ||
                backendRoadmap.flatMap(n => n.children || []).flatMap(n => n?.children || []).find(n => n?.id === nodeId);
    
    if (node) {
      setSelectedNode(node);
      setIsModalOpen(true);
    }
  };

  const handleCompleteNode = () => {
    if (selectedNode) {
      completeNode(selectedNode.id);
      setIsModalOpen(false);
    }
  };

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <Header />
      
      {/* Hero Section */}
      <div className="bg-gradient-to-br from-blue-600 to-blue-800 dark:from-blue-900 dark:to-blue-950">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24">
          <div className="text-center">
            <h1 className="text-4xl font-bold text-white mb-4">
              Backend Development Roadmap
            </h1>
            <p className="text-xl text-white/90 mb-8">
              Step by step guide to becoming a modern backend developer
            </p>
          </div>
        </div>
      </div>

      {/* Content */}
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        {/* Progress Tracker */}
        <div className="bg-white dark:bg-gray-800 rounded-xl p-6 mb-12 shadow-sm">
          <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
            <div className="flex items-center">
              <div className="w-12 h-12 bg-blue-100 dark:bg-blue-900 rounded-xl flex items-center justify-center mr-4">
                <Code className="h-6 w-6 text-blue-600 dark:text-blue-400" />
              </div>
              <div>
                <div className="text-2xl font-bold text-gray-900 dark:text-white">0%</div>
                <div className="text-sm text-gray-500 dark:text-gray-400">Completed</div>
              </div>
            </div>
            <div className="flex items-center">
              <div className="w-12 h-12 bg-green-100 dark:bg-green-900 rounded-xl flex items-center justify-center mr-4">
                <Book className="h-6 w-6 text-green-600 dark:text-green-400" />
              </div>
              <div>
                <div className="text-2xl font-bold text-gray-900 dark:text-white">15</div>
                <div className="text-sm text-gray-500 dark:text-gray-400">Topics</div>
              </div>
            </div>
            <div className="flex items-center">
              <div className="w-12 h-12 bg-purple-100 dark:bg-purple-900 rounded-xl flex items-center justify-center mr-4">
                <Server className="h-6 w-6 text-purple-600 dark:text-purple-400" />
              </div>
              <div>
                <div className="text-2xl font-bold text-gray-900 dark:text-white">4</div>
                <div className="text-sm text-gray-500 dark:text-gray-400">Main Sections</div>
              </div>
            </div>
            <div className="flex items-center">
              <div className="w-12 h-12 bg-yellow-100 dark:bg-yellow-900 rounded-xl flex items-center justify-center mr-4">
                <Database className="h-6 w-6 text-yellow-600 dark:text-yellow-400" />
              </div>
              <div>
                <div className="text-2xl font-bold text-gray-900 dark:text-white">20+</div>
                <div className="text-sm text-gray-500 dark:text-gray-400">Resources</div>
              </div>
            </div>
          </div>
        </div>

        {/* Roadmap Content */}
        <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm">
          <div className="p-6 border-b border-gray-200 dark:border-gray-700">
            <div className="flex items-center justify-between">
              <h2 className="text-xl font-bold text-gray-900 dark:text-white">
                Learning Path
              </h2>
              <div className="text-sm text-gray-500 dark:text-gray-400">
                Click on a topic to learn more
              </div>
            </div>
          </div>
          
          <RoadmapFlow
            nodes={nodes}
            edges={edges}
            onNodeClick={handleNodeClick}
          />
        </div>
      </div>

      <RoadmapModal
        isOpen={isModalOpen}
        onClose={() => setIsModalOpen(false)}
        node={selectedNode}
        onComplete={handleCompleteNode}
        isCompleted={selectedNode ? completedNodes.includes(selectedNode.id) : false}
      />
    </div>
  );
};

export default RoadmapPage;

/== RoadmapsListPage.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Code, Server, Database, Cloud, Globe, File as Mobile, Brain, Shield } from 'lucide-react';
import Header from '../components/organisms/Header';

const RoadmapsListPage: React.FC = () => {
  const roadmaps = [
    {
      id: 'backend',
      title: 'Backend Development',
      description: 'Step by step guide to becoming a modern backend developer',
      icon: Server,
      color: 'bg-blue-500',
      progress: 0
    },
    {
      id: 'frontend',
      title: 'Frontend Development',
      description: 'Step by step guide to becoming a modern frontend developer',
      icon: Code,
      color: 'bg-purple-500',
      progress: 0
    },
    {
      id: 'devops',
      title: 'DevOps',
      description: 'Step by step guide to becoming a DevOps engineer',
      icon: Cloud,
      color: 'bg-green-500',
      progress: 0
    },
    {
      id: 'fullstack',
      title: 'Full Stack Development',
      description: 'Step by step guide to becoming a full stack developer',
      icon: Globe,
      color: 'bg-indigo-500',
      progress: 0
    }
  ];

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <Header />
      {/* Hero Section */}
      <div className="bg-gradient-to-br from-gray-900 to-gray-800">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24">
          <div className="text-center">
            <h1 className="text-4xl font-bold text-white mb-4">
              Developer Roadmaps
            </h1>
            <p className="text-xl text-gray-300 mb-8">
              Step by step guides and paths to learn different tools or technologies
            </p>
          </div>
        </div>
      </div>

      {/* Roadmaps Grid */}
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          {roadmaps.map((roadmap) => (
            <Link
              key={roadmap.id}
              to={`/roadmap/${roadmap.id}`}
              className="group"
            >
              <div className="bg-white dark:bg-gray-800 rounded-xl p-6 shadow-sm hover:shadow-lg transition-all duration-200 h-full">
                <div className="flex items-center mb-4">
                  <div className={`${roadmap.color} p-3 rounded-xl`}>
                    <roadmap.icon className="h-6 w-6 text-white" />
                  </div>
                  {roadmap.progress > 0 && (
                    <div className="ml-auto">
                      <div className="text-sm font-medium text-gray-900 dark:text-white">
                        {roadmap.progress}% Complete
                      </div>
                    </div>
                  )}
                </div>
                <h3 className="text-xl font-bold text-gray-900 dark:text-white mb-2">
                  {roadmap.title}
                </h3>
                <p className="text-gray-600 dark:text-gray-300 mb-4">
                  {roadmap.description}
                </p>
                <div className="flex items-center text-sm text-gray-500 dark:text-gray-400">
                  <div className="flex items-center">
                    <div className="w-2 h-2 rounded-full bg-green-500 mr-2" />
                    <span>Beginner friendly</span>
                  </div>
                  <div className="flex items-center ml-4">
                    <div className="w-2 h-2 rounded-full bg-yellow-500 mr-2" />
                    <span>Interactive</span>
                  </div>
                </div>
              </div>
            </Link>
          ))}
        </div>
      </div>
    </div>
  );
};

export default RoadmapsListPage;

/== SubmissionPage.tsx
import React, { useState } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Upload, AlertCircle, CheckCircle } from 'lucide-react';
import Button from '../components/atoms/Button';
import SubmissionGuidance from '../components/organisms/SubmissionGuidance';
import SubmissionChecklist from '../components/molecules/SubmissionChecklist';

const SubmissionPage: React.FC = () => {
  const { courseId, lessonId } = useParams<{ courseId: string; lessonId: string }>();
  const navigate = useNavigate();
  const [showGuidance, setShowGuidance] = useState(true);
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [checkedItems, setCheckedItems] = useState<string[]>([]);
  const [showChecklist, setShowChecklist] = useState(false);

  const checklistItems = [
    { id: 'tested', label: 'I have tested all features locally', required: true },
    { id: 'guidelines', label: 'My code follows the project guidelines', required: true },
    { id: 'docs', label: 'I have included necessary documentation', required: true },
    { id: 'clean', label: 'Code is clean and well-formatted', required: true },
    { id: 'tests', label: 'All tests are passing', required: true }
  ];

  const handleSubmit = async () => {
    if (!checkedItems.length) {
      setShowChecklist(true);
      return;
    }

    setIsSubmitting(true);
    // Simulate submission
    await new Promise(resolve => setTimeout(resolve, 2000));
    setIsSubmitting(false);
    navigate(`/courses/${courseId}/lessons/${lessonId}/review`);
  };

  const handleChecklistItemToggle = (id: string) => {
    setCheckedItems(prev => 
      prev.includes(id) 
        ? prev.filter(item => item !== id)
        : [...prev, id]
    );
  };

  if (showGuidance) {
    return (
      <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
        <SubmissionGuidance />
        <div className="fixed bottom-8 right-8">
          <Button
            variant="primary"
            onClick={() => setShowGuidance(false)}
            className="bg-blue-600 hover:bg-blue-700"
          >
            Continue to Submission
          </Button>
        </div>
      </div>
    );
  }

  if (showChecklist) {
    return (
      <div className="min-h-screen bg-gray-50 dark:bg-gray-900 p-4">
        <div className="max-w-2xl mx-auto">
          <SubmissionChecklist
            items={checklistItems}
            checkedItems={checkedItems}
            onItemCheck={handleChecklistItemToggle}
          />
          <div className="mt-6 flex justify-end space-x-4">
            <Button
              variant="outline"
              onClick={() => setShowChecklist(false)}
              className="text-gray-600 hover:text-gray-900"
            >
              Back
            </Button>
            <Button
              variant="primary"
              onClick={handleSubmit}
              disabled={checkedItems.length !== checklistItems.length}
              className="bg-blue-600 hover:bg-blue-700"
            >
              Continue
            </Button>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <div className="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm overflow-hidden">
          <div className="p-6">
            <div className="flex items-center justify-between mb-8">
              <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
                Submit Your Project
              </h1>
              <Button
                variant="outline"
                onClick={() => setShowGuidance(true)}
                className="text-gray-600 hover:text-gray-900"
              >
                View Guidelines
              </Button>
            </div>

            <div className="space-y-6">
              {/* Repository URL */}
              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Repository URL
                </label>
                <input
                  type="url"
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white"
                  placeholder="https://github.com/username/repository"
                />
              </div>

              {/* Deployment URL */}
              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Deployment URL (Optional)
                </label>
                <input
                  type="url"
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white"
                  placeholder="https://your-app.example.com"
                />
              </div>

              {/* Notes */}
              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Additional Notes
                </label>
                <textarea
                  rows={4}
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white"
                  placeholder="Any additional information about your submission..."
                />
              </div>

              {/* Submit Button */}
              <div className="flex justify-end space-x-4">
                <Button
                  variant="outline"
                  onClick={() => navigate(-1)}
                  className="text-gray-600 hover:text-gray-900"
                >
                  Cancel
                </Button>
                <Button
                  variant="primary"
                  onClick={() => setShowChecklist(true)}
                  disabled={isSubmitting}
                  className="bg-blue-600 hover:bg-blue-700"
                >
                  {isSubmitting ? (
                    <>
                      <Upload className="animate-bounce h-5 w-5 mr-2" />
                      Submitting...
                    </>
                  ) : (
                    'Continue'
                  )}
                </Button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default SubmissionPage;

/== SubmissionReviewPage.tsx
import React, { useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { useSubmissionStore } from '../store/submissionStore';
import SubmissionReview from '../components/organisms/SubmissionReview';

const SubmissionReviewPage: React.FC = () => {
  const { courseId, lessonId } = useParams<{ courseId: string; lessonId: string }>();
  const navigate = useNavigate();
  const { status, feedback, reviewerNotes, submissionFiles, setStatus } = useSubmissionStore();

  useEffect(() => {
    // Simulate API call to get submission status
    const timer = setTimeout(() => {
      setStatus('reviewing');
    }, 1000);

    return () => clearTimeout(timer);
  }, []);

  const handleResubmit = async () => {
    navigate(`/courses/${courseId}/lessons/${lessonId}/submit`);
  };

  if (!status) {
    return (
      <div className="min-h-screen bg-gray-50 dark:bg-gray-900 flex items-center justify-center">
        <div className="animate-spin rounded-full h-12 w-12 border-4 border-blue-500 border-t-transparent"></div>
      </div>
    );
  }

  return (
    <SubmissionReview
      status={status}
      feedback={feedback}
      reviewerNotes={reviewerNotes || undefined}
      submissionFiles={submissionFiles}
      onResubmit={handleResubmit}
    />
  );
};

export default SubmissionReviewPage;

/== TutorialPage.tsx
import React from 'react';
import { useParams } from 'react-router-dom';
import MainLayout from '../components/templates/MainLayout';
import HeroSection from '../components/organisms/HeroSection';
import TutorialHeader from '../components/organisms/TutorialHeader';
import TableOfContents from '../components/molecules/TableOfContents';
import TutorialIntroduction from '../components/organisms/TutorialIntroduction';
import TutorialResources from '../components/organisms/TutorialResources';
import GettingStarted from '../components/organisms/GettingStarted';
import RelatedCourses from '../components/organisms/RelatedCourses';
import { tutorialData } from '../data/tutorials';

const TutorialPage = () => {
  const { language } = useParams<{ language: string }>();
  const tutorial = tutorialData[language || 'python'] || tutorialData.python;

  return (
    <MainLayout>
      {/* Hero Section */}
      <HeroSection
        title={tutorial.title}
        description={tutorial.description}
        image={tutorial.image}
        primaryAction={{
          text: 'Start Learning',
          link: tutorial.topics[0]?.path || '#'
        }}
        secondaryAction={{
          text: 'Try Online Compiler',
          link: '/compiler'
        }}
        background={tutorial.color}
      />

      {/* Breadcrumb Navigation */}
      <TutorialHeader
        language={language || 'python'}
        title={tutorial.title}
      />

      {/* Tutorial Content */}
      <section className="py-12">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="grid grid-cols-1 lg:grid-cols-4 gap-8">
            {/* Sidebar */}
            <div className="lg:col-span-1">
              <TableOfContents topics={tutorial.topics} />
            </div>

            {/* Main Content */}
            <div className="lg:col-span-3">
              <TutorialIntroduction
                title={tutorial.title}
                introduction={tutorial.introduction}
                benefits={tutorial.benefits}
                language={language || 'python'}
              />

              <TutorialResources resources={tutorial.resources} />

              <GettingStarted
                steps={tutorial.gettingStarted}
                firstTutorialPath={tutorial.topics[0]?.path || '#'}
              />
            </div>
          </div>
        </div>
      </section>

      {/* Related Courses */}
      <section className="py-12 bg-gray-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <RelatedCourses courses={tutorial.relatedCourses} />
        </div>
      </section>
    </MainLayout>
  );
};

export default TutorialPage;

/== bundle-detail.tsx
import React, { useEffect, useState } from 'react';
import { useParams, Link } from 'react-router-dom';
// import { CustomerLayout } from '../components/templates/CustomerLayout';
import  Badge  from '../components/atoms/Badge';
import  Button  from '../components/atoms/Button';
// import { ProgressStats } from '../components/molecules/ProgressStats';
import useBundleStore from '../store/useBundleStore';

const BundleDetailPage: React.FC = () => {
  const { id } = useParams<{ id: string }>();
  const { bundles, courses, fetchBundles, fetchCourses, isLoading } = useBundleStore();
  const [bundle, setBundle] = useState<any>(null);
  const [bundleCourses, setBundleCourses] = useState<any[]>([]);

  useEffect(() => {
    fetchBundles();
    fetchCourses();
  }, [fetchBundles, fetchCourses]);

  useEffect(() => {
    if (bundles.length > 0 && id) {
      const foundBundle = bundles.find(b => b.id === id);
      if (foundBundle) {
        setBundle(foundBundle);
        
        // Get the courses in this bundle
        if (courses.length > 0) {
          const coursesInBundle = courses.filter(course => 
            foundBundle.courses.includes(course.id)
          );
          setBundleCourses(coursesInBundle);
        }
      }
    }
  }, [bundles, courses, id]);

  const getTotalDuration = () => {
    if (!bundleCourses) return 0;
    return bundleCourses.reduce((total, course) => total + course.duration, 0);
  };

  if (isLoading || !bundle) {
    return (
     // <CustomerLayout>
        <div className="flex justify-center items-center h-64">
          <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
        </div>
      // </CustomerLayout>
    );
  }

  return (
    // <CustomerLayout>
    <div>
      <div className="bg-gray-50 rounded-lg px-6 py-8 mb-8">
        <div className="max-w-4xl mx-auto">
          <div className="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
            <div>
              <div className="flex space-x-2 mb-2">
                <Badge variant={
                  bundle.difficultyLevel === 'beginner' ? 'info' : 
                  bundle.difficultyLevel === 'intermediate' ? 'warning' : 'danger'
                }>
                  {bundle.difficultyLevel}
                </Badge>
                <Badge variant="info">{bundle.theme}</Badge>
                {bundle.limitedTime && (
                  <Badge variant="warning">Limited Time</Badge>
                )}
              </div>
              <h1 className="text-3xl font-bold text-gray-900 mb-2">{bundle.title}</h1>
              <p className="text-gray-600">{bundle.description}</p>
            </div>
            <div className="mt-4 md:mt-0 text-right">
              <div className="text-3xl font-bold text-blue-600">${bundle.bundlePrice.toFixed(2)}</div>
              <div className="text-gray-500 line-through">${bundle.originalPrice.toFixed(2)}</div>
              <div className="text-sm text-green-600 font-medium">Save {bundle.discountPercentage}%</div>
            </div>
          </div>
          
          <div className="flex flex-col md:flex-row gap-4">
            <div className="flex-1">
              <div className="flex items-center justify-between mb-2">
                <div className="text-gray-600">{bundleCourses.length} Courses</div>
                <div className="text-gray-600">{getTotalDuration()} hours total</div>
              </div>
              <div className="w-full bg-gray-200 rounded-full h-2.5 mb-6">
                <div
                  className="h-2.5 rounded-full bg-blue-600"
                  style={{ width: '0%' }}
                ></div>
              </div>
            </div>
            <div>
              <Link to="/checkout">
                <Button className="w-full md:w-auto">Enroll in Bundle</Button>
              </Link>
            </div>
          </div>
        </div>
      </div>
      
      <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
        <div className="md:col-span-2">
          <div className="bg-white rounded-lg shadow-sm border p-6 mb-8">
            <h2 className="text-xl font-semibold text-gray-900 mb-6">Bundle Contents</h2>
            <div className="space-y-6">
              {bundleCourses.map((course, index) => (
                <div key={course.id} className="border rounded-lg overflow-hidden">
                  <div className="bg-gray-50 p-4 border-b flex justify-between items-center">
                    <div className="flex items-center">
                      <div className="mr-3 text-gray-400 font-medium">{index + 1}</div>
                      <h3 className="font-medium text-gray-900">{course.title}</h3>
                    </div>
                    <Badge variant={
                      course.level === 'beginner' ? 'info' : 
                      course.level === 'intermediate' ? 'warning' : 'danger'
                    }>
                      {course.level}
                    </Badge>
                  </div>
                  <div className="p-4">
                    <p className="text-gray-600 mb-4">{course.description}</p>
                    <div className="flex justify-between text-sm text-gray-500">
                      <div>{course.duration} hours</div>
                      <div>${course.price.toFixed(2)}</div>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>
          
          {bundle.hasCertificate && (
            <div className="bg-white rounded-lg shadow-sm border p-6 mb-8">
              <div className="flex">
                <div className="mr-4 text-blue-500">
                  <svg xmlns="http://www.w3.org/2000/svg" className="h-12 w-12" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
                  </svg>
                </div>
                <div>
                  <h2 className="text-xl font-semibold text-gray-900 mb-2">Bundle Certificate</h2>
                  <p className="text-gray-600">Complete all courses in this bundle to receive a special certificate of completion.</p>
                </div>
              </div>
            </div>
          )}
        </div>
        
        <div>
          <div className="bg-white rounded-lg shadow-sm border p-6 mb-6">
            <h3 className="text-lg font-medium text-gray-900 mb-4">Bundle Includes</h3>
            <ul className="space-y-3">
              <li className="flex items-start">
                <svg className="h-5 w-5 text-green-500 mr-2 mt-0.5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                </svg>
                <span className="text-gray-700">{bundleCourses.length} complete courses</span>
              </li>
              <li className="flex items-start">
                <svg className="h-5 w-5 text-green-500 mr-2 mt-0.5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                </svg>
                <span className="text-gray-700">{getTotalDuration()} hours of content</span>
              </li>
              <li className="flex items-start">
                <svg className="h-5 w-5 text-green-500 mr-2 mt-0.5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                </svg>
                <span className="text-gray-700">
                  {bundle.accessDuration === 'lifetime' 
                    ? 'Lifetime access' 
                    : `${bundle.accessDuration} days access`}
                </span>
              </li>
              {bundle.hasCertificate && (
                <li className="flex items-start">
                  <svg className="h-5 w-5 text-green-500 mr-2 mt-0.5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                    <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                  </svg>
                  <span className="text-gray-700">Certificate of completion</span>
                </li>
              )}
              <li className="flex items-start">
                <svg className="h-5 w-5 text-green-500 mr-2 mt-0.5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                </svg>
                <span className="text-gray-700">Learn at your own pace</span>
              </li>
            </ul>
          </div>
          
          <div className="bg-white rounded-lg shadow-sm border p-6">
            <h3 className="text-lg font-medium text-gray-900 mb-4">Bundle Savings</h3>
            <div className="space-y-4">
              <div>
                <div className="text-sm text-gray-500 mb-1">Courses if purchased separately</div>
                <div className="text-lg font-medium">${bundle.originalPrice.toFixed(2)}</div>
              </div>
              <div>
                <div className="text-sm text-gray-500 mb-1">Bundle price</div>
                <div className="text-lg font-medium text-blue-600">${bundle.bundlePrice.toFixed(2)}</div>
              </div>
              <div>
                <div className="text-sm text-gray-500 mb-1">You save</div>
                <div className="text-lg font-medium text-green-600">
                  ${(bundle.originalPrice - bundle.bundlePrice).toFixed(2)} ({bundle.discountPercentage}%)
                </div>
              </div>
              <Link to="/checkout">
                <Button className="w-full mt-4">Buy Now</Button>
              </Link>
            </div>
          </div>
        </div>
      </div>
      </div>
    // </CustomerLayout>
  );
};

export default BundleDetailPage;

/== bundles.tsx
import React, { useEffect, useState } from 'react';
import { Link } from 'react-router-dom';
// import  CustomerLayout  from '../components/templates/CustomerLayout';
import Badge  from '../components/atoms/Badge';
import Button  from '../components/atoms/Button';
import useBundleStore from '../store/useBundleStore';

const BundlesPage: React.FC = () => {
  const { bundles, courses, fetchBundles, fetchCourses, isLoading } = useBundleStore();
  const [filter, setFilter] = useState<string>('all');
  const [filterThemes, setFilterThemes] = useState<string[]>([]);

  useEffect(() => {
    fetchBundles();
    fetchCourses();
  }, [fetchBundles, fetchCourses]);

  useEffect(() => {
    // Extract unique themes for filter
    if (bundles.length > 0) {
      const themes = [...new Set(bundles.map(bundle => bundle.theme))];
      setFilterThemes(themes);
    }
  }, [bundles]);

  const filteredBundles = bundles.filter(bundle => {
    if (!bundle.isActive) return false;
    if (filter === 'all') return true;
    return bundle.theme === filter;
  });

  const getBundleCoursesCount = (bundleId: string) => {
    const bundle = bundles.find(b => b.id === bundleId);
    return bundle?.courses.length || 0;
  };

  const getTotalDuration = (bundleId: string) => {
    const bundle = bundles.find(b => b.id === bundleId);
    
    if (!bundle || !bundle.courses) return 0;
    
    return bundle.courses.reduce((total, courseId) => {
      const course = courses.find(c => c.id === courseId);
      return total + (course?.duration || 0);
    }, 0);
  };

  if (isLoading) {
    return (
      // <CustomerLayout>
        <div className="flex justify-center items-center h-64">
          <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
        </div>
      // </CustomerLayout> 
    );
  }

  return (
    // <CustomerLayout>
    <div>
      <div className="mb-8">
        <h1 className="text-3xl font-bold text-gray-900 mb-2">Course Bundles</h1>
        <p className="text-lg text-gray-600">Save by purchasing our expertly curated course bundles</p>
      </div>
      
      <div className="flex flex-col md:flex-row gap-8">
        <div className="md:w-1/4">
          <div className="bg-white dark:bg-dark-800 dark:bg-gray-800 rounded-lg shadow-sm border p-4 sticky top-6">
            <h2 className="text-lg font-medium text-gray-900 mb-4">Filter by Theme</h2>
            <div className="space-y-2">
              <div
                className={`p-2 rounded cursor-pointer ${
                  filter === 'all' ? 'bg-blue-50 text-blue-700' : 'hover:bg-gray-50'
                }`}
                onClick={() => setFilter('all')}
              >
                All Bundles
              </div>
              {filterThemes.map(theme => (
                <div
                  key={theme}
                  className={`p-2 rounded cursor-pointer ${
                    filter === theme ? 'bg-blue-50 text-blue-700' : 'hover:bg-gray-50'
                  }`}
                  onClick={() => setFilter(theme)}
                >
                  {theme}
                </div>
              ))}
            </div>
          </div>
        </div>
        
        <div className="md:w-3/4">
          {filteredBundles.length === 0 ? (
            <div className="bg-white dark:bg-dark-800 p-6 rounded-lg border text-center">
              <h3 className="text-lg font-medium text-gray-900 mb-2">No bundles found</h3>
              <p className="text-gray-500">Try adjusting your filters or check back later</p>
            </div>
          ) : (
            <div className="space-y-6">
              {filteredBundles.map(bundle => (
                <div key={bundle.id} className="bg-white dark:bg-dark-800 rounded-lg shadow-sm border overflow-hidden">
                  <div className="md:flex">
                    <div className="md:w-1/3 bg-gray-800 p-6">
                      <div className="mb-4">
                        <Badge variant={
                          bundle.difficultyLevel === 'beginner' ? 'info' : 
                          bundle.difficultyLevel === 'intermediate' ? 'warning' : 'danger'
                        }>
                          {bundle.difficultyLevel}
                        </Badge>
                        {bundle.limitedTime && (
                          <Badge variant="warning" className="ml-2">Limited Time</Badge>
                        )}
                      </div>
                      <h2 className="text-xl font-bold text-gray-900 mb-2">{bundle.title}</h2>
                      <p className="text-gray-600 mb-4">{bundle.description}</p>
                      
                      <div className="flex justify-between mb-4">
                        <div>
                          <div className="text-sm text-gray-500">Total Duration</div>
                          <div className="font-medium">{getTotalDuration(bundle.id)} hours</div>
                        </div>
                        <div>
                          <div className="text-sm text-gray-500">Courses</div>
                          <div className="font-medium">{getBundleCoursesCount(bundle.id)} courses</div>
                        </div>
                      </div>
                      
                      <div className="flex items-end">
                        <div className="text-2xl font-bold text-blue-600">${bundle.bundlePrice.toFixed(2)}</div>
                        <div className="ml-2 text-sm text-gray-500 line-through">${bundle.originalPrice.toFixed(2)}</div>
                      </div>
                      <div className="text-sm text-green-600 font-medium">Save {bundle.discountPercentage}%</div>
                    </div>
                    
                    <div className="md:w-2/3 p-6">
                      <h3 className="text-lg font-medium text-gray-900 mb-4">Courses Included</h3>
                      <ul className="divide-y divide-gray-200 mb-6">
                        {bundle.courses.slice(0, 3).map(courseId => {
                          const course = courses.find(c => c.id === courseId);
                          return course ? (
                            <li key={course.id} className="py-3 flex justify-between items-center">
                              <div>
                                <div className="font-medium">{course.title}</div>
                                <div className="text-sm text-gray-500">{course.level} · {course.duration} hours</div>
                              </div>
                              <div className="text-gray-600">${course.price.toFixed(2)}</div>
                            </li>
                          ) : null;
                        })}
                      </ul>
                      {bundle.courses.length > 3 && (
                        <div className="text-center mb-6">
                          <Link to={`/bundles/${bundle.id}`} className="text-blue-600 hover:text-blue-800">
                            + {bundle.courses.length - 3} more courses
                          </Link>
                        </div>
                      )}
                      
                      <div className="flex space-x-4">
                        <Link to={`/bundles/${bundle.id}`} className="flex-1">
                          <Button variant="outline" className="w-full">View Details</Button>
                        </Link>
                        <Link to="/checkout" className="flex-1">
                          <Button variant="primary" className="w-full">Buy Bundle</Button>
                        </Link>
                      </div>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          )}
        </div>
      </div>
      </div>
    // </CustomerLayout>
  );
};

export default BundlesPage;

/== cms/AdminContentEditor.tsx
import React, { useState } from 'react';
import { useEditor, EditorContent } from '@tiptap/react';
import StarterKit from '@tiptap/starter-kit';
import Image from '@tiptap/extension-image';
import Link from '@tiptap/extension-link';
import { Save, Image as ImageIcon, Link as LinkIcon, List, ListOrdered, Bold, Italic, Code } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';

const AdminContentEditor = () => {
  const [title, setTitle] = useState('');
  const [saving, setSaving] = useState(false);

  const editor = useEditor({
    extensions: [
      StarterKit,
      Image,
      Link.configure({
        openOnClick: false
      })
    ],
    content: '<p>Start writing your content here...</p>',
  });

  const handleSave = async () => {
    setSaving(true);
    // Simulate saving
    await new Promise(resolve => setTimeout(resolve, 1000));
    setSaving(false);
  };

  return (
    <div className="max-w-5xl mx-auto">
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Content Editor</h1>
        <Button
          variant="primary"
          onClick={handleSave}
          disabled={saving}
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Save className="h-5 w-5 mr-2" />
          {saving ? 'Saving...' : 'Save'}
        </Button>
      </div>

      <Card className="mb-6">
        <div className="p-6">
          <input
            type="text"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            placeholder="Enter title..."
            className="w-full text-2xl font-bold border-0 border-b-2 border-gray-200 dark:border-gray-700 pb-2 mb-6 bg-transparent focus:ring-0 focus:border-brand-green"
          />

          <div className="border border-gray-200 dark:border-gray-700 rounded-lg overflow-hidden">
            {/* Editor Toolbar */}
            <div className="border-b border-gray-200 dark:border-gray-700 p-2 bg-gray-50 dark:bg-gray-800">
              <div className="flex flex-wrap gap-2">
                <button
                  onClick={() => editor?.chain().focus().toggleBold().run()}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('bold') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <Bold className="h-5 w-5" />
                </button>
                <button
                  onClick={() => editor?.chain().focus().toggleItalic().run()}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('italic') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <Italic className="h-5 w-5" />
                </button>
                <button
                  onClick={() => editor?.chain().focus().toggleCode().run()}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('code') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <Code className="h-5 w-5" />
                </button>
                <button
                  onClick={() => editor?.chain().focus().toggleBulletList().run()}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('bulletList') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <List className="h-5 w-5" />
                </button>
                <button
                  onClick={() => editor?.chain().focus().toggleOrderedList().run()}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('orderedList') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <ListOrdered className="h-5 w-5" />
                </button>
                <button
                  onClick={() => {
                    const url = window.prompt('Enter image URL');
                    if (url) {
                      editor?.chain().focus().setImage({ src: url }).run();
                    }
                  }}
                  className="p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700"
                >
                  <ImageIcon className="h-5 w-5" />
                </button>
                <button
                  onClick={() => {
                    const url = window.prompt('Enter link URL');
                    if (url) {
                      editor?.chain().focus().setLink({ href: url }).run();
                    }
                  }}
                  className={`p-2 rounded hover:bg-gray-200 dark:hover:bg-gray-700 ${
                    editor?.isActive('link') ? 'bg-gray-200 dark:bg-gray-700' : ''
                  }`}
                >
                  <LinkIcon className="h-5 w-5" />
                </button>
              </div>
            </div>

            {/* Editor Content */}
            <div className="p-4 min-h-[400px] prose dark:prose-invert max-w-none">
              <EditorContent editor={editor} />
            </div>
          </div>
        </div>
      </Card>
    </div>
  );
};

export default AdminContentEditor;

/== cms/AdminContentPreview.tsx
import React from 'react';

const AdminContentPreview = () => {
  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-6">Content Preview</h1>
      <div className="bg-white rounded-lg shadow p-6">
        <p className="text-gray-600">Content preview implementation coming soon...</p>
      </div>
    </div>
  );
};

export default AdminContentPreview;

/== cms/AdminCourseForm.tsx
import React, { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Save, ArrowLeft, Plus, Image as ImageIcon, Link as LinkIcon, List, ListOrdered } from 'lucide-react';
import { useEditor, EditorContent } from '@tiptap/react';
import StarterKit from '@tiptap/starter-kit';
import Image from '@tiptap/extension-image';
import Link from '@tiptap/extension-link';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';
import { useCourseStore } from '../../store/courseStore';
import { DragDropContext, Droppable, Draggable } from 'react-beautiful-dnd';

const AdminCourseForm: React.FC = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const { getCourseById } = useCourseStore();
  
  const [formData, setFormData] = useState({
    title: '',
    description: '',
    level: 'beginner',
    duration: '',
    price: '',
    image: '',
    features: [''],
    curriculum: [
      { id: '1', title: 'Introduction', type: 'lesson', content: '' },
    ]
  });

  const editor = useEditor({
    extensions: [
      StarterKit,
      Image,
      Link.configure({
        openOnClick: false
      })
    ],
    content: '',
    onUpdate: ({ editor }) => {
      // Handle content update
      console.log(editor.getHTML());
    }
  });

  useEffect(() => {
    if (id) {
      const course = getCourseById(id);
      if (course) {
        setFormData({
          title: course.title,
          description: course.description,
          level: course.level.toLowerCase(),
          duration: course.duration,
          price: course.price,
          image: course.image,
          features: course.features,
          curriculum: [{ id: '1', title: 'Introduction', type: 'lesson', content: '' }]
        });
      }
    }
  }, [id]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    navigate('/cms/courses');
  };

  const addCurriculumItem = () => {
    setFormData(prev => ({
      ...prev,
      curriculum: [
        ...prev.curriculum,
        {
          id: String(prev.curriculum.length + 1),
          title: 'New Lesson',
          type: 'lesson',
          content: ''
        }
      ]
    }));
  };

  const handleDragEnd = (result: any) => {
    if (!result.destination) return;

    const items = Array.from(formData.curriculum);
    const [reorderedItem] = items.splice(result.source.index, 1);
    items.splice(result.destination.index, 0, reorderedItem);

    setFormData(prev => ({ ...prev, curriculum: items }));
  };

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <div className="flex items-center">
          <Button
            variant="outline"
            onClick={() => navigate('/cms/courses')}
            className="mr-4"
          >
            <ArrowLeft className="h-5 w-5 mr-2" />
            Back
          </Button>
          <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
            {id ? 'Edit Course' : 'Create Course'}
          </h1>
        </div>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        {/* Main Form */}
        <div className="lg:col-span-2">
          <Card className="overflow-hidden">
            <form onSubmit={handleSubmit} className="p-6 space-y-6">
              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Course Title
                </label>
                <input
                  type="text"
                  value={formData.title}
                  onChange={(e) => setFormData(prev => ({ ...prev, title: e.target.value }))}
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  required
                />
              </div>

              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Description
                </label>
                <textarea
                  value={formData.description}
                  onChange={(e) => setFormData(prev => ({ ...prev, description: e.target.value }))}
                  rows={4}
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  required
                />
              </div>

              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    Level
                  </label>
                  <select
                    value={formData.level}
                    onChange={(e) => setFormData(prev => ({ ...prev, level: e.target.value }))}
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  >
                    <option value="beginner">Beginner</option>
                    <option value="intermediate">Intermediate</option>
                    <option value="advanced">Advanced</option>
                  </select>
                </div>

                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    Duration
                  </label>
                  <input
                    type="text"
                    value={formData.duration}
                    onChange={(e) => setFormData(prev => ({ ...prev, duration: e.target.value }))}
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                    placeholder="e.g. 8 weeks"
                    required
                  />
                </div>
              </div>

              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Course Image URL
                </label>
                <input
                  type="url"
                  value={formData.image}
                  onChange={(e) => setFormData(prev => ({ ...prev, image: e.target.value }))}
                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  required
                />
              </div>

              {/* Rich Text Editor */}
              <div>
                <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Course Content
                </label>
                <Card className="p-4 bg-white dark:bg-gray-800">
                  <div className="border-b border-gray-200 dark:border-gray-700 pb-4 mb-4">
                    <div className="flex items-center space-x-2">
                      <button
                        type="button"
                        onClick={() => editor?.chain().focus().toggleBold().run()}
                        className={`p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700 ${
                          editor?.isActive('bold') ? 'bg-gray-100 dark:bg-gray-700' : ''
                        }`}
                      >
                        <strong>B</strong>
                      </button>
                      <button
                        type="button"
                        onClick={() => editor?.chain().focus().toggleItalic().run()}
                        className={`p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700 ${
                          editor?.isActive('italic') ? 'bg-gray-100 dark:bg-gray-700' : ''
                        }`}
                      >
                        <em>I</em>
                      </button>
                      <button
                        type="button"
                        onClick={() => editor?.chain().focus().toggleBulletList().run()}
                        className={`p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700 ${
                          editor?.isActive('bulletList') ? 'bg-gray-100 dark:bg-gray-700' : ''
                        }`}
                      >
                        <List className="h-5 w-5" />
                      </button>
                      <button
                        type="button"
                        onClick={() => editor?.chain().focus().toggleOrderedList().run()}
                        className={`p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700 ${
                          editor?.isActive('orderedList') ? 'bg-gray-100 dark:bg-gray-700' : ''
                        }`}
                      >
                        <ListOrdered className="h-5 w-5" />
                      </button>
                      <button
                        type="button"
                        onClick={() => {
                          const url = window.prompt('Enter image URL');
                          if (url) {
                            editor?.chain().focus().setImage({ src: url }).run();
                          }
                        }}
                        className="p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700"
                      >
                        <ImageIcon className="h-5 w-5" />
                      </button>
                      <button
                        type="button"
                        onClick={() => {
                          const url = window.prompt('Enter link URL');
                          if (url) {
                            editor?.chain().focus().setLink({ href: url }).run();
                          }
                        }}
                        className={`p-2 rounded hover:bg-gray-100 dark:hover:bg-gray-700 ${
                          editor?.isActive('link') ? 'bg-gray-100 dark:bg-gray-700' : ''
                        }`}
                      >
                        <LinkIcon className="h-5 w-5" />
                      </button>
                    </div>
                  </div>
                  <EditorContent editor={editor} className="prose dark:prose-invert max-w-none min-h-[200px]" />
                </Card>
              </div>

              {/* Curriculum Builder */}
              <div>
                <div className="flex items-center justify-between mb-4">
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300">
                    Curriculum
                  </label>
                  <Button
                    type="button"
                    variant="outline"
                    onClick={addCurriculumItem}
                    className="text-sm"
                  >
                    <Plus className="h-4 w-4 mr-2" />
                    Add Lesson
                  </Button>
                </div>
                
                <DragDropContext onDragEnd={handleDragEnd}>
                  <Droppable droppableId="curriculum">
                    {(provided) => (
                      <div
                        {...provided.droppableProps}
                        ref={provided.innerRef}
                        className="space-y-2"
                      >
                        {formData.curriculum.map((item, index) => (
                          <Draggable key={item.id} draggableId={item.id} index={index}>
                            {(provided) => (
                              <div
                                ref={provided.innerRef}
                                {...provided.draggableProps}
                                {...provided.dragHandleProps}
                                className="bg-white dark:bg-gray-800 rounded-lg border border-gray-200 dark:border-gray-700 p-4"
                              >
                                <input
                                  type="text"
                                  value={item.title}
                                  onChange={(e) => {
                                    const newCurriculum = [...formData.curriculum];
                                    newCurriculum[index].title = e.target.value;
                                    setFormData(prev => ({ ...prev, curriculum: newCurriculum }));
                                  }}
                                  className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                                  placeholder="Lesson title"
                                />
                              </div>
                            )}
                          </Draggable>
                        ))}
                        {provided.placeholder}
                      </div>
                    )}
                  </Droppable>
                </DragDropContext>
              </div>

              <div className="flex justify-end pt-6 border-t border-gray-200 dark:border-gray-700">
                <Button
                  type="submit"
                  variant="primary"
                  className="bg-brand-green hover:bg-brand-green/90"
                >
                  <Save className="h-5 w-5 mr-2" />
                  {id ? 'Update Course' : 'Create Course'}
                </Button>
              </div>
            </form>
          </Card>
        </div>

        {/* Preview Panel */}
        <div className="lg:col-span-1">
          <Card className="sticky top-6">
            <div className="p-6">
              <h2 className="text-lg font-medium text-gray-900 dark:text-white mb-4">
                Course Preview
              </h2>
              <div className="aspect-video bg-gray-100 dark:bg-gray-700 rounded-lg mb-4">
                {formData.image && (
                  <img
                    src={formData.image}
                    alt={formData.title}
                    className="w-full h-full object-cover rounded-lg"
                  />
                )}
              </div>
              <div className="space-y-4">
                <div>
                  <h3 className="font-medium text-gray-900 dark:text-white">
                    {formData.title || 'Course Title'}
                  </h3>
                  <p className="text-sm text-gray-500 dark:text-gray-400">
                    {formData.description || 'Course description will appear here'}
                  </p>
                </div>
                <div>
                  <div className="text-sm text-gray-500 dark:text-gray-400">Level</div>
                  <div className="font-medium text-gray-900 dark:text-white capitalize">
                    {formData.level}
                  </div>
                </div>
                <div>
                  <div className="text-sm text-gray-500 dark:text-gray-400">Duration</div>
                  <div className="font-medium text-gray-900 dark:text-white">
                    {formData.duration || 'Not specified'}
                  </div>
                </div>
                <div>
                  <div className="text-sm text-gray-500 dark:text-gray-400">Curriculum</div>
                  <div className="font-medium text-gray-900 dark:text-white">
                    {formData.curriculum.length} lessons
                  </div>
                </div>
              </div>
            </div>
          </Card>
        </div>
      </div>
    </div>
  );
};

export default AdminCourseForm;

/== cms/AdminCourseList.tsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { Plus, Pencil, Trash, Search } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';
import { useCourseStore } from '../../store/courseStore';

const AdminCourseList: React.FC = () => {
  const navigate = useNavigate();
  const { listCourses } = useCourseStore();
  const [searchQuery, setSearchQuery] = useState('');

  const filteredCourses = listCourses.filter(course =>
    course.title.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
          Manage Courses
        </h1>
        <Button
          variant="primary"
          onClick={() => navigate('/cms/courses/create')}
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Course
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search courses..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Course
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Level
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Students
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Rating
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredCourses.map((course) => (
                <tr key={course.id}>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex items-center">
                      <img
                        src={course.image}
                        alt={course.title}
                        className="h-10 w-10 rounded-lg object-cover"
                      />
                      <div className="ml-4">
                        <div className="text-sm font-medium text-gray-900 dark:text-white">
                          {course.title}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {course.duration}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">
                      {course.level}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {course.students.toLocaleString()}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {course.rating}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button
                      onClick={() => navigate(`/cms/courses/edit/${course.id}`)}
                      className="text-blue-600 hover:text-blue-900 dark:text-blue-400 dark:hover:text-blue-300 mr-4"
                    >
                      <Pencil className="h-5 w-5" />
                    </button>
                    <button className="text-red-600 hover:text-red-900 dark:text-red-400 dark:hover:text-red-300">
                      <Trash className="h-5 w-5" />
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminCourseList;

/== cms/AdminCurriculumBuilder.tsx
import  { useState } from 'react';
import { Plus, Save, Trash, GripVertical, ChevronRight, ChevronDown, File, FolderPlus } from 'lucide-react';
import Button from '../../components/atoms/Button';
import { DragDropContext, Droppable, Draggable } from 'react-beautiful-dnd';

interface Module {
  id: string;
  title: string;
  lessons: Lesson[];
  isExpanded?: boolean;
}

interface Lesson {
  id: string;
  title: string;
  type: 'content' | 'quiz' | 'assignment';
  duration: string;
}

const AdminCurriculumBuilder = () => {
  const [modules, setModules] = useState<Module[]>([
    {
      id: 'module-1',
      title: 'Introduction to Programming',
      isExpanded: true,
      lessons: [
        { id: 'lesson-1', title: 'What is Programming?', type: 'content', duration: '10 min' },
        { id: 'lesson-2', title: 'Basic Concepts Quiz', type: 'quiz', duration: '15 min' }
      ]
    }
  ]);

  const addModule = () => {
    const newModule: Module = {
      id: `module-${Date.now()}`,
      title: 'New Module',
      isExpanded: true,
      lessons: []
    };
    setModules([...modules, newModule]);
  };

  const addLesson = (moduleId: string) => {
    setModules(modules.map(module => {
      if (module.id === moduleId) {
        return {
          ...module,
          lessons: [
            ...module.lessons,
            {
              id: `lesson-${Date.now()}`,
              title: 'New Lesson',
              type: 'content',
              duration: '0 min'
            }
          ]
        };
      }
      return module;
    }));
  };

  const toggleModule = (moduleId: string) => {
    setModules(modules.map(module => {
      if (module.id === moduleId) {
        return { ...module, isExpanded: !module.isExpanded };
      }
      return module;
    }));
  };

  const handleDragEnd = (result: any) => {
    if (!result.destination) return;

    const { source, destination, type } = result;

    // Handle module reordering
    if (type === 'module') {
      const reorderedModules = Array.from(modules);
      const [removed] = reorderedModules.splice(source.index, 1);
      reorderedModules.splice(destination.index, 0, removed);
      setModules(reorderedModules);
      return;
    }

    // Handle lesson reordering
    const sourceModule = modules.find(m => m.id === source.droppableId);
    const destModule = modules.find(m => m.id === destination.droppableId);

    if (!sourceModule || !destModule) return;

    const newModules = [...modules];
    const sourceModuleIndex = modules.findIndex(m => m.id === source.droppableId);
    const destModuleIndex = modules.findIndex(m => m.id === destination.droppableId);

    if (source.droppableId === destination.droppableId) {
      // Reordering within the same module
      const newLessons = Array.from(sourceModule.lessons);
      const [removed] = newLessons.splice(source.index, 1);
      newLessons.splice(destination.index, 0, removed);
      newModules[sourceModuleIndex].lessons = newLessons;
    } else {
      // Moving between modules
      const sourceLessons = Array.from(sourceModule.lessons);
      const destLessons = Array.from(destModule.lessons);
      const [removed] = sourceLessons.splice(source.index, 1);
      destLessons.splice(destination.index, 0, removed);
      newModules[sourceModuleIndex].lessons = sourceLessons;
      newModules[destModuleIndex].lessons = destLessons;
    }

    setModules(newModules);
  };

  return (
    <div className="max-w-4xl mx-auto p-6">
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Curriculum Builder</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Save className="h-5 w-5 mr-2" />
          Save Curriculum
        </Button>
      </div>

      <DragDropContext onDragEnd={handleDragEnd}>
        <div className="space-y-6">
          <Droppable droppableId="modules-list" type="module">
            {(provided) => (
              <div
                ref={provided.innerRef}
                {...provided.droppableProps}
              >
                {modules.map((module, moduleIndex) => (
                  <Draggable key={module.id} draggableId={module.id} index={moduleIndex}>
                    {(provided) => (
                      <div
                        ref={provided.innerRef}
                        {...provided.draggableProps}
                        className="bg-white dark:bg-gray-800 rounded-lg shadow-sm border border-gray-200 dark:border-gray-700 mb-4"
                      >
                        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
                          <div className="flex items-center">
                            <div {...provided.dragHandleProps} className="mr-2">
                              <GripVertical className="h-5 w-5 text-gray-400" />
                            </div>
                            <button
                              onClick={() => toggleModule(module.id)}
                              className="mr-2"
                            >
                              {module.isExpanded ? (
                                <ChevronDown className="h-5 w-5" />
                              ) : (
                                <ChevronRight className="h-5 w-5" />
                              )}
                            </button>
                            <input
                              type="text"
                              value={module.title}
                              onChange={(e) => {
                                const newModules = [...modules];
                                newModules[moduleIndex].title = e.target.value;
                                setModules(newModules);
                              }}
                              className="flex-1 font-medium bg-transparent border-0 focus:ring-0"
                              placeholder="Module Title"
                            />
                            <Button
                              variant="outline"
                              onClick={() => addLesson(module.id)}
                              className="ml-4 text-sm"
                            >
                              <Plus className="h-4 w-4 mr-2" />
                              Add Lesson
                            </Button>
                            <button
                              onClick={() => {
                                setModules(modules.filter(m => m.id !== module.id));
                              }}
                              className="ml-4 text-red-500 hover:text-red-600"
                            >
                              <Trash className="h-5 w-5" />
                            </button>
                          </div>
                        </div>

                        {module.isExpanded && (
                          <Droppable droppableId={module.id} type="lesson">
                            {(provided) => (
                              <div
                                ref={provided.innerRef}
                                {...provided.droppableProps}
                                className="p-4"
                              >
                                <div className="space-y-3">
                                  {module.lessons.map((lesson, lessonIndex) => (
                                    <Draggable
                                      key={lesson.id}
                                      draggableId={lesson.id}
                                      index={lessonIndex}
                                    >
                                      {(provided) => (
                                        <div
                                          ref={provided.innerRef}
                                          {...provided.draggableProps}
                                          className="flex items-center bg-gray-50 dark:bg-gray-800 rounded-lg p-3"
                                        >
                                          <div {...provided.dragHandleProps} className="mr-2">
                                            <GripVertical className="h-5 w-5 text-gray-400" />
                                          </div>
                                          <File className="h-5 w-5 text-gray-400 mr-2" />
                                          <input
                                            type="text"
                                            value={lesson.title}
                                            onChange={(e) => {
                                              const newModules = [...modules];
                                              newModules[moduleIndex].lessons[lessonIndex].title = e.target.value;
                                              setModules(newModules);
                                            }}
                                            className="flex-1 bg-transparent border-0 focus:ring-0"
                                            placeholder="Lesson Title"
                                          />
                                          <select
                                            value={lesson.type}
                                            onChange={(e) => {
                                              const newModules = [...modules];
                                              newModules[moduleIndex].lessons[lessonIndex].type = e.target.value as 'content' | 'quiz' | 'assignment';
                                              setModules(newModules);
                                            }}
                                            className="mx-2 border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                                          >
                                            <option value="content">Content</option>
                                            <option value="quiz">Quiz</option>
                                            <option value="assignment">Assignment</option>
                                          </select>
                                          <input
                                            type="text"
                                            value={lesson.duration}
                                            onChange={(e) => {
                                              const newModules = [...modules];
                                              newModules[moduleIndex].lessons[lessonIndex].duration = e.target.value;
                                              setModules(newModules);
                                            }}
                                            className="w-20 bg-transparent border-0 focus:ring-0 text-right"
                                            placeholder="Duration"
                                          />
                                          <button
                                            onClick={() => {
                                              const newModules = [...modules];
                                              newModules[moduleIndex].lessons = newModules[moduleIndex].lessons.filter(
                                                (_, idx) => idx !== lessonIndex
                                              );
                                              setModules(newModules);
                                            }}
                                            className="ml-2 text-red-500 hover:text-red-600"
                                          >
                                            <Trash className="h-5 w-5" />
                                          </button>
                                        </div>
                                      )}
                                    </Draggable>
                                  ))}
                                  {provided.placeholder}
                                </div>
                              </div>
                            )}
                          </Droppable>
                        )}
                      </div>
                    )}
                  </Draggable>
                ))}
                {provided.placeholder}
              </div>
            )}
          </Droppable>
        </div>
      </DragDropContext>

      <div className="mt-6">
        <Button
          variant="outline"
          onClick={addModule}
          className="w-full"
        >
          <FolderPlus className="h-5 w-5 mr-2" />
          Add Module
        </Button>
      </div>
    </div>
  );
};

export default AdminCurriculumBuilder;

/== cms/AdminDashboard.tsx
import React from 'react';
import { Book, Users, Star, TrendingUp, ArrowUp, ArrowDown, Clock, Award } from 'lucide-react';
import Card from '../../components/atoms/Card';

const AdminDashboard: React.FC = () => {
  const stats = [
    { 
      label: 'Total Courses', 
      value: '24', 
      icon: Book, 
      color: 'bg-blue-500/90 dark:bg-blue-600',
      trend: { value: '+12%', up: true }
    },
    { 
      label: 'Active Students', 
      value: '1,234', 
      icon: Users, 
      color: 'bg-green-500/90 dark:bg-green-600',
      trend: { value: '+8%', up: true }
    },
    { 
      label: 'Average Rating', 
      value: '4.8', 
      icon: Star, 
      color: 'bg-yellow-500/90 dark:bg-yellow-600',
      trend: { value: '-2%', up: false }
    },
    { 
      label: 'Monthly Growth', 
      value: '+12%', 
      icon: TrendingUp, 
      color: 'bg-purple-500/90 dark:bg-purple-600',
      trend: { value: '+5%', up: true }
    },
  ];

  const recentCourses = [
    {
      title: 'Advanced JavaScript',
      students: 234,
      rating: 4.9,
      image: 'https://images.unsplash.com/photo-1579468118864-1b9ea3c0db4a?ixlib=rb-1.2.1&auto=format&fit=crop&w=180&q=60'
    },
    {
      title: 'Python for Data Science',
      students: 189,
      rating: 4.8,
      image: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?ixlib=rb-1.2.1&auto=format&fit=crop&w=180&q=60'
    },
    {
      title: 'React Fundamentals',
      students: 156,
      rating: 4.7,
      image: 'https://images.unsplash.com/photo-1633356122544-f134324a6cee?ixlib=rb-1.2.1&auto=format&fit=crop&w=180&q=60'
    }
  ];

  const recentUsers = [
    {
      name: 'Sarah Chen',
      email: 'sarah@example.com',
      joinDate: '2 days ago',
      courses: 3
    },
    {
      name: 'Michael Rodriguez',
      email: 'michael@example.com',
      joinDate: '3 days ago',
      courses: 2
    },
    {
      name: 'Emma Wilson',
      email: 'emma@example.com',
      joinDate: '5 days ago',
      courses: 4
    }
  ];

  return (
    <div className="space-y-8">
      {/* Welcome Section */}
      <div className="bg-gradient-to-r from-brand-green/90 to-emerald-600/90 dark:from-brand-green/80 dark:to-emerald-600/80 rounded-xl p-8 text-white backdrop-blur-sm shadow-lg">
        <h1 className="text-3xl font-bold mb-2">Welcome back, Admin! 👋</h1>
        <p className="text-white/90 text-lg">Here's what's happening with your courses today.</p>
      </div>

      {/* Stats Grid */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        {stats.map((stat, index) => (
          <Card 
            key={index} 
            className="p-6 hover:shadow-lg transition-all duration-300 backdrop-blur-sm bg-white/50 dark:bg-gray-800/50 border border-gray-200/20 dark:border-gray-700/20"
          >
            <div className="flex items-center justify-between mb-4">
              <div className={`${stat.color} p-3 rounded-xl shadow-lg backdrop-blur-sm`}>
                <stat.icon className="h-6 w-6 text-white" />
              </div>
              <div className={`flex items-center px-3 py-1.5 rounded-full text-sm font-medium ${
                stat.trend.up 
                  ? 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400'
                  : 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400'
              }`}>
                {stat.trend.up ? (
                  <ArrowUp className="h-4 w-4 mr-1" />
                ) : (
                  <ArrowDown className="h-4 w-4 mr-1" />
                )}
                {stat.trend.value}
              </div>
            </div>
            <div>
              <p className="text-3xl font-bold text-gray-900 dark:text-white mb-1">
                {stat.value}
              </p>
              <p className="text-sm font-medium text-gray-600 dark:text-gray-400">
                {stat.label}
              </p>
            </div>
          </Card>
        ))}
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        {/* Recent Courses */}
        <Card className="overflow-hidden backdrop-blur-sm bg-white/50 dark:bg-gray-800/50 border border-gray-200/20 dark:border-gray-700/20">
          <div className="p-6 border-b border-gray-200/20 dark:border-gray-700/20">
            <h2 className="text-xl font-bold text-gray-900 dark:text-white">Recent Courses</h2>
          </div>
          <div className="p-6">
            <div className="space-y-6">
              {recentCourses.map((course, index) => (
                <div key={index} className="flex items-center p-4 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-700/50 transition-colors duration-200">
                  <img
                    src={course.image}
                    alt={course.title}
                    className="w-12 h-12 rounded-lg object-cover shadow-md"
                  />
                  <div className="ml-4 flex-1">
                    <h3 className="text-sm font-semibold text-gray-900 dark:text-white">
                      {course.title}
                    </h3>
                    <div className="flex items-center mt-1 text-sm text-gray-600 dark:text-gray-400">
                      <Users className="h-4 w-4 mr-1" />
                      {course.students} students
                      <span className="mx-2">•</span>
                      <Star className="h-4 w-4 mr-1 text-yellow-400 fill-current" />
                      {course.rating}
                    </div>
                  </div>
                  <button className="text-brand-green hover:text-brand-green/90 font-medium">
                    View
                  </button>
                </div>
              ))}
            </div>
          </div>
        </Card>

        {/* Recent Users */}
        <Card className="overflow-hidden backdrop-blur-sm bg-white/50 dark:bg-gray-800/50 border border-gray-200/20 dark:border-gray-700/20">
          <div className="p-6 border-b border-gray-200/20 dark:border-gray-700/20">
            <h2 className="text-xl font-bold text-gray-900 dark:text-white">Recent Users</h2>
          </div>
          <div className="p-6">
            <div className="space-y-6">
              {recentUsers.map((user, index) => (
                <div key={index} className="flex items-center p-4 rounded-lg hover:bg-gray-50 dark:hover:bg-gray-700/50 transition-colors duration-200">
                  <div className="w-12 h-12 rounded-full bg-gradient-to-br from-brand-green to-emerald-600 flex items-center justify-center text-white font-bold text-lg shadow-lg">
                    {user.name.charAt(0)}
                  </div>
                  <div className="ml-4 flex-1">
                    <h3 className="text-sm font-semibold text-gray-900 dark:text-white">
                      {user.name}
                    </h3>
                    <div className="flex items-center mt-1 text-sm text-gray-600 dark:text-gray-400">
                      <Clock className="h-4 w-4 mr-1" />
                      {user.joinDate}
                      <span className="mx-2">•</span>
                      <Award className="h-4 w-4 mr-1" />
                      {user.courses} courses
                    </div>
                  </div>
                  <button className="text-brand-green hover:text-brand-green/90 font-medium">
                    View
                  </button>
                </div>
              ))}
            </div>
          </div>
        </Card>
      </div>
    </div>
  );
};

export default AdminDashboard;

/== cms/AdminMediaLibrary.tsx
import React, { useState } from 'react';
import { Upload, Search, Grid, List as ListIcon, Trash } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';

const AdminMediaLibrary = () => {
  const [view, setView] = useState<'grid' | 'list'>('grid');
  const [searchQuery, setSearchQuery] = useState('');

  const mockMedia = [
    {
      id: '1',
      name: 'hero-image.jpg',
      url: 'https://images.unsplash.com/photo-1542831371-29b0f74f9713',
      size: '2.4 MB',
      type: 'image/jpeg',
      uploadedAt: '2024-03-20'
    },
    {
      id: '2',
      name: 'course-thumbnail.jpg',
      url: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97',
      size: '1.8 MB',
      type: 'image/jpeg',
      uploadedAt: '2024-03-19'
    },
    {
      id: '3',
      name: 'programming-basics.jpg',
      url: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935',
      size: '1.2 MB',
      type: 'image/jpeg',
      uploadedAt: '2024-03-18'
    }
  ];

  const filteredMedia = mockMedia.filter(item =>
    item.name.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Media Library</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Upload className="h-5 w-5 mr-2" />
          Upload Media
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="flex items-center justify-between">
            <div className="relative flex-1 max-w-md">
              <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
              <input
                type="text"
                placeholder="Search media..."
                value={searchQuery}
                onChange={(e) => setSearchQuery(e.target.value)}
                className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              />
            </div>
            <div className="flex items-center space-x-2">
              <button
                onClick={() => setView('grid')}
                className={`p-2 rounded-lg ${
                  view === 'grid'
                    ? 'bg-gray-100 dark:bg-gray-700'
                    : 'hover:bg-gray-100 dark:hover:bg-gray-700'
                }`}
              >
                <Grid className="h-5 w-5" />
              </button>
              <button
                onClick={() => setView('list')}
                className={`p-2 rounded-lg ${
                  view === 'list'
                    ? 'bg-gray-100 dark:bg-gray-700'
                    : 'hover:bg-gray-100 dark:hover:bg-gray-700'
                }`}
              >
                <ListIcon className="h-5 w-5" />
              </button>
            </div>
          </div>
        </div>

        <div className="p-6">
          {view === 'grid' ? (
            <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
              {filteredMedia.map((item) => (
                <div
                  key={item.id}
                  className="group relative bg-gray-100 dark:bg-gray-800 rounded-lg overflow-hidden aspect-square"
                >
                  <img
                    src={item.url}
                    alt={item.name}
                    className="w-full h-full object-cover"
                  />
                  <div className="absolute inset-0 bg-black bg-opacity-50 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center">
                    <div className="text-white text-center p-4">
                      <p className="font-medium mb-2">{item.name}</p>
                      <p className="text-sm">{item.size}</p>
                      <button className="mt-4 text-red-500 hover:text-red-400">
                        <Trash className="h-5 w-5" />
                      </button>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          ) : (
            <div className="overflow-x-auto">
              <table className="w-full">
                <thead>
                  <tr className="text-left text-sm font-medium text-gray-500 dark:text-gray-400">
                    <th className="px-4 py-2">File</th>
                    <th className="px-4 py-2">Type</th>
                    <th className="px-4 py-2">Size</th>
                    <th className="px-4 py-2">Uploaded</th>
                    <th className="px-4 py-2">Actions</th>
                  </tr>
                </thead>
                <tbody>
                  {filteredMedia.map((item) => (
                    <tr key={item.id} className="border-t border-gray-200 dark:border-gray-700">
                      <td className="px-4 py-4">
                        <div className="flex items-center">
                          <div className="h-10 w-10 flex-shrink-0">
                            <img
                              src={item.url}
                              alt={item.name}
                              className="h-10 w-10 rounded object-cover"
                            />
                          </div>
                          <div className="ml-4">
                            <div className="font-medium text-gray-900 dark:text-white">
                              {item.name}
                            </div>
                          </div>
                        </div>
                      </td>
                      <td className="px-4 py-4 text-sm text-gray-500 dark:text-gray-400">
                        {item.type}
                      </td>
                      <td className="px-4 py-4 text-sm text-gray-500 dark:text-gray-400">
                        {item.size}
                      </td>
                      <td className="px-4 py-4 text-sm text-gray-500 dark:text-gray-400">
                        {item.uploadedAt}
                      </td>
                      <td className="px-4 py-4 text-sm">
                        <button className="text-red-500 hover:text-red-400">
                          <Trash className="h-5 w-5" />
                        </button>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          )}
        </div>
      </Card>
    </div>
  );
};

export default AdminMediaLibrary;

/== cms/AdminQuizBuilder.tsx
import React, { useState } from 'react';
import { Plus, Save, Trash, GripVertical } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';
import { DragDropContext, Droppable, Draggable } from 'react-beautiful-dnd';

interface Question {
  id: string;
  text: string;
  type: 'multiple' | 'single';
  options: { id: string; text: string; isCorrect: boolean }[];
}

const AdminQuizBuilder = () => {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');
  const [questions, setQuestions] = useState<Question[]>([
    {
      id: '1',
      text: 'What is a variable?',
      type: 'single',
      options: [
        { id: '1', text: 'A container for storing data', isCorrect: true },
        { id: '2', text: 'A type of loop', isCorrect: false },
        { id: '3', text: 'A function name', isCorrect: false },
        { id: '4', text: 'A programming language', isCorrect: false }
      ]
    }
  ]);

  const addQuestion = () => {
    const newQuestion: Question = {
      id: String(questions.length + 1),
      text: '',
      type: 'single',
      options: [
        { id: '1', text: '', isCorrect: false },
        { id: '2', text: '', isCorrect: false }
      ]
    };
    setQuestions([...questions, newQuestion]);
  };

  const addOption = (questionId: string) => {
    setQuestions(questions.map(q => {
      if (q.id === questionId) {
        return {
          ...q,
          options: [
            ...q.options,
            {
              id: String(q.options.length + 1),
              text: '',
              isCorrect: false
            }
          ]
        };
      }
      return q;
    }));
  };

  const handleDragEnd = (result: any) => {
    if (!result.destination) return;

    const items = Array.from(questions);
    const [reorderedItem] = items.splice(result.source.index, 1);
    items.splice(result.destination.index, 0, reorderedItem);

    setQuestions(items);
  };

  return (
    <div className="max-w-4xl mx-auto">
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Quiz Builder</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Save className="h-5 w-5 mr-2" />
          Save Quiz
        </Button>
      </div>

      <Card className="mb-6">
        <div className="p-6">
          <input
            type="text"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            placeholder="Quiz Title"
            className="w-full text-2xl font-bold border-0 border-b-2 border-gray-200 dark:border-gray-700 pb-2 mb-4 bg-transparent focus:ring-0 focus:border-brand-green"
          />
          <textarea
            value={description}
            onChange={(e) => setDescription(e.target.value)}
            placeholder="Quiz Description"
            rows={3}
            className="w-full border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
          />
        </div>
      </Card>

      <DragDropContext onDragEnd={handleDragEnd}>
        <Droppable droppableId="questions">
          {(provided) => (
            <div
              {...provided.droppableProps}
              ref={provided.innerRef}
              className="space-y-6"
            >
              {questions.map((question, index) => (
                <Draggable key={question.id} draggableId={question.id} index={index}>
                  {(provided) => (
                    <div
                      ref={provided.innerRef}
                      {...provided.draggableProps}
                      className="relative"
                    >
                      <Card>
                        <div className="absolute left-4 top-1/2 -translate-y-1/2" {...provided.dragHandleProps}>
                          <GripVertical className="h-5 w-5 text-gray-400" />
                        </div>
                        <div className="p-6 pl-12">
                          <div className="flex items-center justify-between mb-4">
                            <input
                              type="text"
                              value={question.text}
                              onChange={(e) => {
                                const newQuestions = [...questions];
                                newQuestions[index].text = e.target.value;
                                setQuestions(newQuestions);
                              }}
                              placeholder="Question text"
                              className="flex-1 text-lg font-medium border-0 border-b-2 border-gray-200 dark:border-gray-700 pb-2 bg-transparent focus:ring-0 focus:border-brand-green"
                            />
                            <select
                              value={question.type}
                              onChange={(e) => {
                                const newQuestions = [...questions];
                                newQuestions[index].type = e.target.value as 'multiple' | 'single';
                                setQuestions(newQuestions);
                              }}
                              className="ml-4 border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                            >
                              <option value="single">Single Choice</option>
                              <option value="multiple">Multiple Choice</option>
                            </select>
                          </div>

                          <div className="space-y-3">
                            {question.options.map((option, optionIndex) => (
                              <div key={option.id} className="flex items-center space-x-3">
                                <input
                                  type={question.type === 'multiple' ? 'checkbox' : 'radio'}
                                  checked={option.isCorrect}
                                  onChange={(e) => {
                                    const newQuestions = [...questions];
                                    if (question.type === 'single') {
                                      newQuestions[index].options = newQuestions[index].options.map(
                                        (opt, idx) => ({ ...opt, isCorrect: idx === optionIndex })
                                      );
                                    } else {
                                      newQuestions[index].options[optionIndex].isCorrect = e.target.checked;
                                    }
                                    setQuestions(newQuestions);
                                  }}
                                  className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                                />
                                <input
                                  type="text"
                                  value={option.text}
                                  onChange={(e) => {
                                    const newQuestions = [...questions];
                                    newQuestions[index].options[optionIndex].text = e.target.value;
                                    setQuestions(newQuestions);
                                  }}
                                  placeholder={`Option ${optionIndex + 1}`}
                                  className="flex-1 border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                                />
                                <button
                                  onClick={() => {
                                    const newQuestions = [...questions];
                                    newQuestions[index].options = newQuestions[index].options.filter(
                                      (_, idx) => idx !== optionIndex
                                    );
                                    setQuestions(newQuestions);
                                  }}
                                  className="text-red-500 hover:text-red-600"
                                >
                                  <Trash className="h-5 w-5" />
                                </button>
                              </div>
                            ))}
                          </div>

                          <div className="mt-4 flex justify-between">
                            <Button
                              variant="outline"
                              onClick={() => addOption(question.id)}
                              className="text-sm"
                            >
                              <Plus className="h-4 w-4 mr-2" />
                              Add Option
                            </Button>
                            <Button
                              variant="outline"
                              onClick={() => {
                                setQuestions(questions.filter(q => q.id !== question.id));
                              }}
                              className="text-sm text-red-500 hover:text-red-600"
                            >
                              <Trash className="h-4 w-4 mr-2" />
                              Delete Question
                            </Button>
                          </div>
                        </div>
                      </Card>
                    </div>
                  )}
                </Draggable>
              ))}
              {provided.placeholder}
            </div>
          )}
        </Droppable>
      </DragDropContext>

      <div className="mt-6">
        <Button
          variant="outline"
          onClick={addQuestion}
          className="w-full"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Question
        </Button>
      </div>
    </div>
  );
};

export default AdminQuizBuilder;

/== cms/AdminSettings.tsx
import React, { useState } from 'react';
import { Save, Mail, Globe, Bell, Shield, Database } from 'lucide-react';
import Card from '../../components/atoms/Card';
import Button from '../../components/atoms/Button';

const AdminSettings: React.FC = () => {
  const [activeTab, setActiveTab] = useState('general');
  const [formData, setFormData] = useState({
    siteName: 'Coder Growth',
    siteUrl: 'https://codergrowth.com',
    adminEmail: 'admin@codergrowth.com',
    emailNotifications: true,
    maintenanceMode: false,
    registrationEnabled: true,
    backupFrequency: 'daily',
    maxUploadSize: '10',
    defaultUserRole: 'user'
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Handle settings update
  };

  const tabs = [
    { id: 'general', label: 'General', icon: Globe },
    { id: 'email', label: 'Email', icon: Mail },
    { id: 'notifications', label: 'Notifications', icon: Bell },
    { id: 'security', label: 'Security', icon: Shield },
    { id: 'system', label: 'System', icon: Database }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
          Settings
        </h1>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-4 gap-6">
        {/* Sidebar */}
        <Card className="lg:col-span-1 overflow-hidden">
          <nav className="space-y-1 p-4">
            {tabs.map((tab) => (
              <button
                key={tab.id}
                onClick={() => setActiveTab(tab.id)}
                className={`
                  w-full flex items-center px-4 py-2 text-sm font-medium rounded-lg
                  ${activeTab === tab.id
                    ? 'bg-brand-green text-white'
                    : 'text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700'
                  }
                `}
              >
                <tab.icon className="h-5 w-5 mr-3" />
                {tab.label}
              </button>
            ))}
          </nav>
        </Card>

        {/* Main Content */}
        <Card className="lg:col-span-3">
          <form onSubmit={handleSubmit} className="p-6 space-y-6">
            {activeTab === 'general' && (
              <>
                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    Site Name
                  </label>
                  <input
                    type="text"
                    value={formData.siteName}
                    onChange={(e) => setFormData(prev => ({ ...prev, siteName: e.target.value }))}
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  />
                </div>

                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    Site URL
                  </label>
                  <input
                    type="url"
                    value={formData.siteUrl}
                    onChange={(e) => setFormData(prev => ({ ...prev, siteUrl: e.target.value }))}
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  />
                </div>

                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    Admin Email
                  </label>
                  <input
                    type="email"
                    value={formData.adminEmail}
                    onChange={(e) => setFormData(prev => ({ ...prev, adminEmail: e.target.value }))}
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  />
                </div>
              </>
            )}

            {activeTab === 'email' && (
              <>
                <div className="bg-yellow-50 dark:bg-yellow-900/30 border border-yellow-200 dark:border-yellow-700 rounded-lg p-4 mb-6">
                  <div className="flex">
                    <Bell className="h-5 w-5 text-yellow-400" />
                    <div className="ml-3">
                      <h3 className="text-sm font-medium text-yellow-800 dark:text-yellow-200">
                        Email Configuration
                      </h3>
                      <p className="mt-2 text-sm text-yellow-700 dark:text-yellow-300">
                        Make sure to configure your SMTP settings properly to ensure email delivery.
                      </p>
                    </div>
                  </div>
                </div>

                <div>
                  <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                    SMTP Host
                  </label>
                  <input
                    type="text"
                    placeholder="smtp.example.com"
                    className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  />
                </div>

                <div className="grid grid-cols-2 gap-4">
                  <div>
                    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                      SMTP Port
                    </label>
                    <input
                      type="number"
                      placeholder="587"
                      className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                    />
                  </div>
                  <div>
                    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                      Encryption
                    </label>
                    <select className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white">
                      <option value="tls">TLS</option>
                      <option value="ssl">SSL</option>
                      <option value="none">None</option>
                    </select>
                  </div>
                </div>
              </>
            )}

            {activeTab === 'notifications' && (
              <>
                <div className="space-y-4">
                  <div className="flex items-center justify-between">
                    <div>
                      <h3 className="text-sm font-medium text-gray-900 dark:text-white">
                        Email Notifications
                      </h3>
                      <p className="text-sm text-gray-500 dark:text-gray-400">
                        Receive email notifications for important events
                      </p>
                    </div>
                    <div className="flex items-center">
                      <input
                        type="checkbox"
                        checked={formData.emailNotifications}
                        onChange={(e) => setFormData(prev => ({ ...prev, emailNotifications: e.target.checked }))}
                        className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                      />
                    </div>
                  </div>

                  <div className="flex items-center justify-between pt-4 border-t border-gray-200 dark:border-gray-700">
                    <div>
                      <h3 className="text-sm font-medium text-gray-900 dark:text-white">
                        New User Registration
                      </h3>
                      <p className="text-sm text-gray-500 dark:text-gray-400">
                        Get notified when a new user registers
                      </p>
                    </div>
                    <div className="flex items-center">
                      <input
                        type="checkbox"
                        className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                      />
                    </div>
                  </div>

                  <div className="flex items-center justify-between pt-4 border-t border-gray-200 dark:border-gray-700">
                    <div>
                      <h3 className="text-sm font-medium text-gray-900 dark:text-white">
                        Course Completion
                      </h3>
                      <p className="text-sm text-gray-500 dark:text-gray-400">
                        Get notified when a user completes a course
                      </p>
                    </div>
                    <div className="flex items-center">
                      <input
                        type="checkbox"
                        className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                      />
                    </div>
                  </div>
                </div>
              </>
            )}

            {activeTab === 'security' && (
              <>
                <div className="space-y-4">
                  <div>
                    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                      Default User Role
                    </label>
                    <select
                      value={formData.defaultUserRole}
                      onChange={(e) => setFormData(prev => ({ ...prev, defaultUserRole: e.target.value }))}
                      className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                    >
                      <option value="user">User</option>
                      <option value="admin">Admin</option>
                    </select>
                  </div>

                  <div className="flex items-center justify-between pt-4 border-t border-gray-200 dark:border-gray-700">
                    <div>
                      <h3 className="text-sm font-medium text-gray-900 dark:text-white">
                        Enable Registration
                      </h3>
                      <p className="text-sm text-gray-500 dark:text-gray-400">
                        Allow new users to register
                      </p>
                    </div>
                    <div className="flex items-center">
                      <input
                        type="checkbox"
                        checked={formData.registrationEnabled}
                        onChange={(e) => setFormData(prev => ({ ...prev, registrationEnabled: e.target.checked }))}
                        className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                      />
                    </div>
                  </div>
                </div>
              </>
            )}

            {activeTab === 'system' && (
              <>
                <div className="space-y-4">
                  <div>
                    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                      Backup Frequency
                    </label>
                    <select
                      value={formData.backupFrequency}
                      onChange={(e) => setFormData(prev => ({ ...prev, backupFrequency: e.target.value }))}
                      className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                    >
                      <option value="daily">Daily</option>
                      <option value="weekly">Weekly</option>
                      <option value="monthly">Monthly</option>
                    </select>
                  </div>

                  <div>
                    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                      Maximum Upload Size (MB)
                    </label>
                    <input
                      type="number"
                      value={formData.maxUploadSize}
                      onChange={(e) => setFormData(prev => ({ ...prev, maxUploadSize: e.target.value }))}
                      className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                    />
                  </div>

                  <div className="flex items-center justify-between pt-4 border-t border-gray-200 dark:border-gray-700">
                    <div>
                      <h3 className="text-sm font-medium text-gray-900 dark:text-white">
                        Maintenance Mode
                      </h3>
                      <p className="text-sm text-gray-500 dark:text-gray-400">
                        Put the site in maintenance mode
                      </p>
                    </div>
                    <div className="flex items-center">
                      <input
                        type="checkbox"
                        checked={formData.maintenanceMode}
                        onChange={(e) => setFormData(prev => ({ ...prev, maintenanceMode: e.target.checked }))}
                        className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
                      />
                    </div>
                  </div>
                </div>
              </>
            )}

            <div className="flex justify-end pt-6 border-t border-gray-200 dark:border-gray-700">
              <Button
                type="submit"
                variant="primary"
                className="bg-brand-green hover:bg-brand-green/90"
              >
                <Save className="h-5 w-5 mr-2" />
                Save Changes
              </Button>
            </div>
          </form>
        </Card>
      </div>
    </div>
  );
};

export default AdminSettings;

/== cms/AdminUserForm.tsx
import React, { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import { Save, ArrowLeft } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';
import { useAuthStore } from '../../store/authStore';

const AdminUserForm: React.FC = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const { mockUsers } = useAuthStore();
  
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    role: 'user' as 'user' | 'admin'
  });

  useEffect(() => {
    if (id) {
      const user = mockUsers.find(u => u.id === id);
      if (user) {
        setFormData({
          name: user.name,
          email: user.email,
          password: '',
          role: user.role
        });
      }
    }
  }, [id]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Handle form submission
    navigate('/cms/users');
  };

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <div className="flex items-center">
          <Button
            variant="outline"
            onClick={() => navigate('/cms/users')}
            className="mr-4"
          >
            <ArrowLeft className="h-5 w-5 mr-2" />
            Back
          </Button>
          <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
            {id ? 'Edit User' : 'Create User'}
          </h1>
        </div>
      </div>

      <Card className="max-w-2xl">
        <form onSubmit={handleSubmit} className="p-6 space-y-6">
          <div>
            <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              Full Name
            </label>
            <input
              type="text"
              value={formData.name}
              onChange={(e) => setFormData(prev => ({ ...prev, name: e.target.value }))}
              className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              required
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              Email Address
            </label>
            <input
              type="email"
              value={formData.email}
              onChange={(e) => setFormData(prev => ({ ...prev, email: e.target.value }))}
              className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              required
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              {id ? 'New Password (leave blank to keep current)' : 'Password'}
            </label>
            <input
              type="password"
              value={formData.password}
              onChange={(e) => setFormData(prev => ({ ...prev, password: e.target.value }))}
              className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              {...(!id && { required: true })}
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              Role
            </label>
            <select
              value={formData.role}
              onChange={(e) => setFormData(prev => ({ ...prev, role: e.target.value as 'user' | 'admin' }))}
              className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            >
              <option value="user">User</option>
              <option value="admin">Admin</option>
            </select>
          </div>

          <div className="flex justify-end pt-6 border-t border-gray-200 dark:border-gray-700">
            <Button
              type="submit"
              variant="primary"
              className="bg-brand-green hover:bg-brand-green/90"
            >
              <Save className="h-5 w-5 mr-2" />
              {id ? 'Update User' : 'Create User'}
            </Button>
          </div>
        </form>
      </Card>
    </div>
  );
};

export default AdminUserForm;

/== cms/AdminUserList.tsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { Plus, Pencil, Trash, Search, Shield } from 'lucide-react';
import Button from '../../components/atoms/Button';
import Card from '../../components/atoms/Card';
import { useAuthStore } from '../../store/authStore';

const AdminUserList: React.FC = () => {
  const navigate = useNavigate();
  const { mockUsers } = useAuthStore();
  const [searchQuery, setSearchQuery] = useState('');

  const filteredUsers = mockUsers.filter(user =>
    user.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
    user.email.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">
          Manage Users
        </h1>
        <Button
          variant="primary"
          onClick={() => navigate('/cms/users/create')}
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add User
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search users..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  User
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Email
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Role
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredUsers.map((user) => (
                <tr key={user.id}>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex items-center">
                      <div className="h-10 w-10 rounded-full bg-brand-green flex items-center justify-center text-white font-medium">
                        {user.name.charAt(0)}
                      </div>
                      <div className="ml-4">
                        <div className="text-sm font-medium text-gray-900 dark:text-white">
                          {user.name}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {user.email}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex items-center">
                      <Shield className={`h-4 w-4 mr-2 ${
                        user.role === 'admin' ? 'text-purple-500' : 'text-blue-500'
                      }`} />
                      <span className={`px-2 inline-flex text-xs leading-5 font-semibold rounded-full ${
                        user.role === 'admin'
                          ? 'bg-purple-100 text-purple-800'
                          : 'bg-blue-100 text-blue-800'
                      }`}>
                        {user.role}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button
                      onClick={() => navigate(`/cms/users/edit/${user.id}`)}
                      className="text-blue-600 hover:text-blue-900 dark:text-blue-400 dark:hover:text-blue-300 mr-4"
                    >
                      <Pencil className="h-5 w-5" />
                    </button>
                    <button className="text-red-600 hover:text-red-900 dark:text-red-400 dark:hover:text-red-300">
                      <Trash className="h-5 w-5" />
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminUserList;

/== cms/IntegrationsPage.tsx
import React, { useState, useEffect } from 'react';
import { Routes, Route, Navigate } from 'react-router-dom';
import { Server, CreditCard, Shield, Globe, Webhook } from 'lucide-react';
import Card from '../../components/atoms/Card';
import Heading from '../../components/atoms/Heading';
import Text from '../../components/atoms/Text';
import IntegrationsDashboard from '../../components/organisms/IntegrationsDashboard';
import IntegrationsIndex from './integrations/IntegrationsIndex';
import AdminApiManagement from './integrations/AdminApiManagement';
import AdminPaymentGateways from './integrations/AdminPaymentGateways';
import AdminSso from './integrations/AdminSso';
import AdminThirdParty from './integrations/AdminThirdParty';
import AdminWebhooks from './integrations/AdminWebhooks';

const IntegrationsPage: React.FC = () => {
  // Mock data untuk statistik integrasi
  const [stats, setStats] = useState({
    apiKeysCount: 2,
    paymentGatewaysCount: 2,
    ssoProvidersCount: 3,
    thirdPartyCount: 3,
    webhooksCount: 3
  });

  // Simulasi pemanggilan API untuk mendapatkan data statistik
  useEffect(() => {
    // Di implementasi nyata, ini akan memanggil API
    // fetchIntegrationStats().then(data => setStats(data));
  }, []);

  return (
    <div className="container mx-auto px-4 py-6">
      <Routes>
        <Route
          index
          element={
            <div>
              <div className="mb-8">
                <Heading level="h1" className="text-2xl font-bold mb-2">Manajemen Integrasi</Heading>
                <Text className="text-gray-600 dark:text-white">
                  Kelola semua integrasi sistem dengan layanan dan aplikasi eksternal.
                </Text>
              </div>
              
              <Card className="mb-8 p-6 bg-gradient-to-r from-brand-green/10 to-blue-500/10">
                <div className="flex flex-col md:flex-row items-start md:items-center justify-between">
                  <div className="mb-4 md:mb-0">
                    <Heading level="h2" className="text-xl font-bold mb-2 text-gray-600 dark:text-white">Pusat Integrasi</Heading>
                    <Text className="text-gray-600 dark:text-white">
                      Hubungkan platform pembelajaran Anda dengan berbagai layanan eksternal untuk meningkatkan fungsionalitas.
                    </Text>
                  </div>
                  <div className="flex space-x-2">
                    <div className="p-2 rounded-full bg-brand-green/20 text-brand-green">
                      <Server className="h-5 w-5" />
                    </div>
                    <div className="p-2 rounded-full bg-brand-green/20 text-brand-green">
                      <CreditCard className="h-5 w-5" />
                    </div>
                    <div className="p-2 rounded-full bg-brand-green/20 text-brand-green">
                      <Shield className="h-5 w-5" />
                    </div>
                    <div className="p-2 rounded-full bg-brand-green/20 text-brand-green">
                      <Globe className="h-5 w-5" />
                    </div>
                    <div className="p-2 rounded-full bg-brand-green/20 text-brand-green">
                      <Webhook className="h-5 w-5" />
                    </div>
                  </div>
                </div>
              </Card>
              
              <IntegrationsDashboard 
                apiKeysCount={stats.apiKeysCount}
                paymentGatewaysCount={stats.paymentGatewaysCount}
                ssoProvidersCount={stats.ssoProvidersCount}
                thirdPartyCount={stats.thirdPartyCount}
                webhooksCount={stats.webhooksCount}
              />
              
              <div className="mt-8">
                <IntegrationsIndex />
              </div>
            </div>
          }
        />
        <Route path="api-management" element={<AdminApiManagement />} />
        <Route path="payment-gateways" element={<AdminPaymentGateways />} />
        <Route path="sso" element={<AdminSso />} />
        <Route path="third-party" element={<AdminThirdParty />} />
        <Route path="webhooks" element={<AdminWebhooks />} />
        <Route path="*" element={<Navigate to="/cms/integrations" replace />} />
      </Routes>
    </div>
  );
};

export default IntegrationsPage;

/== cms/communication/AdminAnnouncements.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Bell, Globe, Users, Calendar } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminAnnouncements = () => {
  const [searchQuery, setSearchQuery] = useState('');

  const mockAnnouncements = [
    {
      id: '1',
      title: 'New Course Launch',
      content: 'We are excited to announce our new Python Advanced course!',
      type: 'global',
      status: 'active',
      publishDate: '2024-03-20',
      audience: 'All Users'
    },
    {
      id: '2',
      title: 'Platform Maintenance',
      content: 'Scheduled maintenance on March 25th',
      type: 'system',
      status: 'scheduled',
      publishDate: '2024-03-25',
      audience: 'All Users'
    },
    {
      id: '3',
      title: 'Course Update',
      content: 'Important updates to JavaScript Fundamentals course',
      type: 'course',
      status: 'draft',
      publishDate: '2024-03-22',
      audience: 'Course Students'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Announcements</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          New Announcement
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search announcements..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Announcement
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Type
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Publish Date
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Audience
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {mockAnnouncements.map((announcement) => (
                <tr key={announcement.id}>
                  <td className="px-6 py-4">
                    <div>
                      <div className="font-medium text-gray-900 dark:text-white">
                        {announcement.title}
                      </div>
                      <div className="text-sm text-gray-500 dark:text-gray-400">
                        {announcement.content}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex items-center">
                      {announcement.type === 'global' && <Globe className="h-5 w-5 text-blue-500 mr-2" />}
                      {announcement.type === 'system' && <Bell className="h-5 w-5 text-yellow-500 mr-2" />}
                      {announcement.type === 'course' && <Users className="h-5 w-5 text-green-500 mr-2" />}
                      <span className="capitalize">{announcement.type}</span>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className={`px-2 inline-flex text-xs leading-5 font-semibold rounded-full ${
                      announcement.status === 'active'
                        ? 'bg-green-100 text-green-800'
                        : announcement.status === 'scheduled'
                        ? 'bg-yellow-100 text-yellow-800'
                        : 'bg-gray-100 text-gray-800'
                    }`}>
                      {announcement.status}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    <div className="flex items-center">
                      <Calendar className="h-5 w-5 mr-2" />
                      {announcement.publishDate}
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {announcement.audience}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminAnnouncements;

/== cms/communication/AdminEmailTemplates.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Mail } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminEmailTemplates = () => {
  const [searchQuery, setSearchQuery] = useState('');

  const mockTemplates = [
    {
      id: '1',
      name: 'Welcome Email',
      subject: 'Welcome to Coder Growth!',
      lastModified: '2024-03-20',
      category: 'Onboarding'
    },
    {
      id: '2',
      name: 'Course Completion',
      subject: 'Congratulations on completing the course!',
      lastModified: '2024-03-19',
      category: 'Notifications'
    },
    {
      id: '3',
      name: 'Password Reset',
      subject: 'Reset your password',
      lastModified: '2024-03-18',
      category: 'Account'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Email Templates</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          New Template
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search templates..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Template Name
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Subject
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Category
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Modified
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {mockTemplates.map((template) => (
                <tr key={template.id}>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex items-center">
                      <Mail className="h-5 w-5 text-gray-400 mr-3" />
                      <span className="font-medium text-gray-900 dark:text-white">
                        {template.name}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {template.subject}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-blue-100 text-blue-800">
                      {template.category}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {template.lastModified}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminEmailTemplates;

/== cms/communication/AdminFeedback.tsx
import React, { useState } from 'react';
import { Search, Star, MessageSquare, ThumbsUp, Filter } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminFeedback = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [selectedCategory, setSelectedCategory] = useState('all');

  const mockFeedback = [
    {
      id: '1',
      user: 'Sarah Chen',
      course: 'Python Basics',
      rating: 5,
      comment: 'Great course! The content was well-structured and easy to follow.',
      date: '2024-03-20',
      helpful: 12,
      category: 'course'
    },
    {
      id: '2',
      user: 'Michael Rodriguez',
      course: 'JavaScript Fundamentals',
      rating: 4,
      comment: 'Good course, but could use more practical examples.',
      date: '2024-03-19',
      helpful: 8,
      category: 'course'
    },
    {
      id: '3',
      user: 'Emma Wilson',
      type: 'Platform Feedback',
      rating: 5,
      comment: 'The new interface is much more intuitive and user-friendly.',
      date: '2024-03-18',
      helpful: 15,
      category: 'platform'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">User Feedback</h1>
        <div className="flex space-x-4">
          <select
            value={selectedCategory}
            onChange={(e) => setSelectedCategory(e.target.value)}
            className="border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white px-4 py-2"
          >
            <option value="all">All Categories</option>
            <option value="course">Course Feedback</option>
            <option value="platform">Platform Feedback</option>
          </select>
          <Button
            variant="outline"
            className="flex items-center"
          >
            <Filter className="h-5 w-5 mr-2" />
            Filter
          </Button>
        </div>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search feedback..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {mockFeedback.map((feedback) => (
            <div key={feedback.id} className="p-6">
              
              <div className="flex items-start justify-between">
                <div className="flex items-start space-x-4">
                  <div className="w-10 h-10 bg-brand-green rounded-full flex items-center justify-center text-white">
                    {feedback.user.charAt(0)}
                  </div>
                  <div>
                    <div className="flex items-center">
                      <h3 className="font-medium text-gray-900 dark:text-white">{feedback.user}</h3>
                      <span className="mx-2 text-gray-300 dark:text-gray-600">•</span>
                      <span className="text-sm text-gray-500">{feedback.date}</span>
                    </div>
                    {feedback.course && (
                      <div className="text-sm text-gray-500 dark:text-gray-400 mt-1">
                        Course: {feedback.course}
                      </div>
                    )}
                    <div className="flex items-center mt-2">
                      {[...Array(5)].map((_, index) => (
                        <Star
                          key={index}
                          className={`h-5 w-5 ${
                            index < feedback.rating
                              ? 'text-yellow-400 fill-current'
                              : 'text-gray-300 dark:text-gray-600'
                          }`}
                        />
                      ))}
                    </div>
                    <p className="mt-2 text-gray-600 dark:text-gray-300">{feedback.comment}</p>
                    <div className="flex items-center mt-4 space-x-4">
                      <button className="flex items-center text-gray-500 hover:text-gray-700">
                        <ThumbsUp className="h-5 w-5 mr-1" />
                        <span>{feedback.helpful}</span>
                      </button>
                      <button className="flex items-center text-gray-500 hover:text-gray-700">
                        <MessageSquare className="h-5 w-5 mr-1" />
                        Reply
                      </button>
                    </div>
                  </div>
                </div>
                <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                  feedback.category === 'course'
                    ? 'bg-blue-100 text-blue-800'
                    : 'bg-purple-100 text-purple-800'
                }`}>
                  {feedback.category === 'course' ? 'Course Feedback' : 'Platform Feedback'}
                </span>
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
};

export default AdminFeedback;

/== cms/communication/AdminMessaging.tsx
import React, { useState } from 'react';
import { Search, MessageSquare, Send, Trash, User, Clock } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminMessaging = () => {
  const [selectedChat, setSelectedChat] = useState<string | null>(null);
  const [message, setMessage] = useState('');

  const mockChats = [
    {
      id: '1',
      user: 'Sarah Chen',
      lastMessage: 'I need help with the Python course',
      timestamp: '2 min ago',
      unread: true
    },
    {
      id: '2',
      user: 'Michael Rodriguez',
      lastMessage: 'Thank you for your help!',
      timestamp: '1 hour ago',
      unread: false
    }
  ];

  const mockMessages = [
    {
      id: '1',
      sender: 'Sarah Chen',
      content: 'I need help with the Python course',
      timestamp: '2 min ago',
      isUser: true
    },
    {
      id: '2',
      sender: 'Admin',
      content: 'Of course! What specific topic are you struggling with?',
      timestamp: '1 min ago',
      isUser: false
    }
  ];

  return (
    <div className="h-[calc(100vh-12rem)]">
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Messaging System</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <MessageSquare className="h-5 w-5 mr-2" />
          New Message
        </Button>
      </div>

      <div className="grid grid-cols-3 gap-6 h-full">
        {/* Chat List */}
        <Card className="col-span-1 overflow-hidden">
          <div className="p-4 border-b border-gray-200 dark:border-gray-700">
            <div className="relative">
              <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
              <input
                type="text"
                placeholder="Search conversations..."
                className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              />
            </div>
          </div>
          <div className="overflow-y-auto h-[calc(100vh-20rem)]">
            {mockChats.map((chat) => (
              <button
                key={chat.id}
                onClick={() => setSelectedChat(chat.id)}
                className={`w-full p-4 text-left hover:bg-gray-50 dark:hover:bg-gray-700/50 border-b border-gray-200 dark:border-gray-700 ${
                  selectedChat === chat.id ? 'bg-gray-50 dark:bg-gray-700/50' : ''
                }`}
              >
                <div className="flex items-center">
                  <div className="w-10 h-10 bg-brand-green rounded-full flex items-center justify-center text-white">
                    {chat.user.charAt(0)}
                  </div>
                  <div className="ml-3 flex-1">
                    <div className="flex items-center justify-between">
                      <span className="font-medium text-gray-900 dark:text-white">{chat.user}</span>
                      <span className="text-xs text-gray-500">{chat.timestamp}</span>
                    </div>
                    <p className="text-sm text-gray-600 dark:text-gray-300 truncate">{chat.lastMessage}</p>
                  </div>
                  {chat.unread && (
                    <div className="w-2 h-2 bg-brand-green rounded-full ml-2"></div>
                  )}
                </div>
              </button>
            ))}
          </div>
        </Card>

        {/* Chat Window */}
        <Card className="col-span-2 flex flex-col">
          {selectedChat ? (
            <>
              <div className="p-4 border-b border-gray-200 dark:border-gray-700">
                <div className="flex items-center justify-between">
                  <div className="flex items-center">
                    <div className="w-10 h-10 bg-brand-green rounded-full flex items-center justify-center text-white">
                      S
                    </div>
                    <div className="ml-3">
                      <h3 className="font-medium text-gray-900 dark:text-white">Sarah Chen</h3>
                      <p className="text-sm text-gray-500">Online</p>
                    </div>
                  </div>
                  <Button variant="outline" className="text-red-500 hover:text-red-600">
                    <Trash className="h-5 w-5" />
                  </Button>
                </div>
              </div>

              <div className="flex-1 overflow-y-auto p-4 space-y-4">
                {mockMessages.map((msg) => (
                  <div
                    key={msg.id}
                    className={`flex ${msg.isUser ? 'justify-end' : 'justify-start'}`}
                  >
                    <div
                      className={`max-w-[70%] rounded-lg p-3 ${
                        msg.isUser
                          ? 'bg-brand-green text-white'
                          : 'bg-gray-100 dark:bg-gray-700'
                      }`}
                    >
                      <p className="text-sm">{msg.content}</p>
                      <div className={`text-xs mt-1 ${msg.isUser ? 'text-white/80' : 'text-gray-500'}`}>
                        {msg.timestamp}
                      </div>
                    </div>
                  </div>
                ))}
              </div>

              <div className="p-4 border-t border-gray-200 dark:border-gray-700">
                <div className="flex items-center space-x-2">
                  <input
                    type="text"
                    value={message}
                    onChange={(e) => setMessage(e.target.value)}
                    placeholder="Type your message..."
                    className="flex-1 px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
                  />
                  <Button variant="primary" className="bg-brand-green hover:bg-brand-green/90">
                    <Send className="h-5 w-5" />
                  </Button>
                </div>
              </div>
            </>
          ) : (
            <div className="flex-1 flex items-center justify-center text-gray-500">
              Select a conversation to start messaging
            </div>
          )}
        </Card>
      </div>
    </div>
  );
};

export default AdminMessaging;

/== cms/communication/AdminNotifications.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Bell, Settings } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminNotifications = () => {
  const [searchQuery, setSearchQuery] = useState('');

  const mockNotifications = [
    {
      id: '1',
      title: 'Course Completion',
      template: 'When a user completes a course',
      type: 'achievement',
      channels: ['email', 'in-app'],
      status: 'active'
    },
    {
      id: '2',
      title: 'New Comment',
      template: 'When someone comments on your post',
      type: 'social',
      channels: ['in-app'],
      status: 'active'
    },
    {
      id: '3',
      title: 'Assignment Due',
      template: 'Assignment deadline reminder',
      type: 'reminder',
      channels: ['email', 'in-app'],
      status: 'inactive'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Notification Settings</h1>
        <div className="flex space-x-4">
          <Button
            variant="outline"
            className="flex items-center"
          >
            <Settings className="h-5 w-5 mr-2" />
            Settings
          </Button>
          <Button
            variant="primary"
            className="bg-brand-green hover:bg-brand-green/90"
          >
            <Plus className="h-5 w-5 mr-2" />
            New Template
          </Button>
        </div>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search notification templates..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Template
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Type
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Channels
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {mockNotifications.map((notification) => (
                <tr key={notification.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Bell className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {notification.title}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {notification.template}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-blue-100 text-blue-800">
                      {notification.type}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="flex space-x-2">
                      {notification.channels.map((channel) => (
                        <span
                          key={channel}
                          className="px-2 py-1 text-xs font-medium bg-gray-100 dark:bg-gray-700 rounded"
                        >
                          {channel}
                        </span>
                      ))}
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <label className="relative inline-flex items-center cursor-pointer">
                      <input
                        type="checkbox"
                        className="sr-only peer"
                        checked={notification.status === 'active'}
                        onChange={() => {}}
                      />
                      <div className="w-11 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-brand-green/20 dark:peer-focus:ring-brand-green/30 rounded-full peer dark:bg-gray-700 peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all dark:border-gray-600 peer-checked:bg-brand-green"></div>
                    </label>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminNotifications;

/== cms/course-features/AdminCertificates.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Award, Download } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminCertificates = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [certificates, setCertificates] = useState([
    {
      id: '1',
      course: 'Python Mastery',
      template: 'Professional Certificate',
      issued: 156,
      lastModified: '2024-03-20',
      status: 'active'
    },
    {
      id: '2',
      course: 'Web Development Bootcamp',
      template: 'Course Completion',
      issued: 89,
      lastModified: '2024-03-19',
      status: 'active'
    },
    {
      id: '3',
      course: 'Data Science Fundamentals',
      template: 'Specialization Certificate',
      issued: 45,
      lastModified: '2024-03-18',
      status: 'draft'
    }
  ]);

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Certificate Management</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Create Template
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search certificates..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Course & Template
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Certificates Issued
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Modified
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {certificates.map((certificate) => (
                <tr key={certificate.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Award className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {certificate.course}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {certificate.template}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="text-sm text-gray-900 dark:text-white font-medium">
                      {certificate.issued}
                    </div>
                    <div className="text-sm text-gray-500 dark:text-gray-400">
                      Total Issued
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {certificate.lastModified}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                      certificate.status === 'active'
                        ? 'bg-green-100 text-green-800'
                        : 'bg-gray-100 text-gray-800'
                    }`}>
                      {certificate.status}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Download className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminCertificates;

/== cms/course-features/AdminCourseCategories.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Tag, ChevronRight } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminCourseCategories = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [categories, setCategories] = useState([
    {
      id: '1',
      name: 'Programming Languages',
      slug: 'programming-languages',
      description: 'Learn various programming languages',
      coursesCount: 12,
      subcategories: ['Python', 'JavaScript', 'Java', 'Go']
    },
    {
      id: '2',
      name: 'Web Development',
      slug: 'web-development',
      description: 'Master web development technologies',
      coursesCount: 8,
      subcategories: ['Frontend', 'Backend', 'Full Stack']
    },
    {
      id: '3',
      name: 'Data Science',
      slug: 'data-science',
      description: 'Explore data science and analytics',
      coursesCount: 6,
      subcategories: ['Machine Learning', 'Data Analysis', 'Statistics']
    }
  ]);

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Course Categories</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Category
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search categories..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Category
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Slug
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Subcategories
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Courses
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {categories.map((category) => (
                <tr key={category.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Tag className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {category.name}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {category.description}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    {category.slug}
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex flex-wrap gap-2">
                      {category.subcategories.map((subcategory, index) => (
                        <span
                          key={index}
                          className="px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded-full"
                        >
                          {subcategory}
                        </span>
                      ))}
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-400">
                    <div className="flex items-center">
                      <span className="font-medium">{category.coursesCount}</span>
                      <ChevronRight className="h-4 w-4 ml-1" />
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminCourseCategories;

/== cms/course-features/AdminCourseScheduling.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Calendar, Clock, Users } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminCourseScheduling = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [schedules, setSchedules] = useState([
    {
      id: '1',
      course: 'Python Basics',
      startDate: '2024-04-01',
      endDate: '2024-05-15',
      enrollmentDeadline: '2024-03-25',
      maxStudents: 50,
      enrolled: 32,
      status: 'upcoming'
    },
    {
      id: '2',
      course: 'JavaScript Fundamentals',
      startDate: '2024-04-15',
      endDate: '2024-05-30',
      enrollmentDeadline: '2024-04-10',
      maxStudents: 40,
      enrolled: 25,
      status: 'upcoming'
    },
    {
      id: '3',
      course: 'Go Programming',
      startDate: '2024-03-01',
      endDate: '2024-04-15',
      enrollmentDeadline: '2024-02-25',
      maxStudents: 45,
      enrolled: 45,
      status: 'in-progress'
    }
  ]);

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Course Scheduling</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Schedule Course
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search schedules..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Course
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Duration
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Enrollment
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {schedules.map((schedule) => (
                <tr key={schedule.id}>
                  <td className="px-6 py-4">
                    <div className="font-medium text-gray-900 dark:text-white">
                      {schedule.course}
                    </div>
                    <div className="text-sm text-gray-500 dark:text-gray-400 flex items-center mt-1">
                      <Calendar className="h-4 w-4 mr-1" />
                      Deadline: {schedule.enrollmentDeadline}
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="text-sm text-gray-500 dark:text-gray-400">
                      <div className="flex items-center">
                        <Clock className="h-4 w-4 mr-1" />
                        Start: {schedule.startDate}
                      </div>
                      <div className="flex items-center mt-1">
                        <Clock className="h-4 w-4 mr-1" />
                        End: {schedule.endDate}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Users className="h-4 w-4 mr-1" />
                      <span className="text-gray-900 dark:text-white font-medium">
                        {schedule.enrolled}/{schedule.maxStudents}
                      </span>
                    </div>
                    <div className="w-full bg-gray-200 rounded-full h-2 mt-2">
                      <div
                        className="bg-brand-green h-2 rounded-full"
                        style={{ width: `${(schedule.enrolled / schedule.maxStudents) * 100}%` }}
                      ></div>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                      schedule.status === 'upcoming'
                        ? 'bg-blue-100 text-blue-800'
                        : schedule.status === 'in-progress'
                        ? 'bg-green-100 text-green-800'
                        : 'bg-gray-100 text-gray-800'
                    }`}>
                      {schedule.status === 'upcoming' ? 'Upcoming' : 'In Progress'}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminCourseScheduling;

/== cms/course-features/AdminPrerequisites.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, BookOpen, ArrowRight } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminPrerequisites = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [prerequisites, setPrerequisites] = useState([
    {
      id: '1',
      course: 'Advanced Python',
      prerequisites: [
        { id: '1', name: 'Python Basics', type: 'course', required: true },
        { id: '2', name: 'Object-Oriented Programming', type: 'skill', required: true }
      ]
    },
    {
      id: '2',
      course: 'React Advanced',
      prerequisites: [
        { id: '3', name: 'JavaScript Fundamentals', type: 'course', required: true },
        { id: '4', name: 'React Basics', type: 'course', required: true },
        { id: '5', name: 'HTML/CSS', type: 'skill', required: false }
      ]
    }
  ]);

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Prerequisites Management</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Prerequisites
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search courses..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {prerequisites.map((item) => (
            <div key={item.id} className="p-6">
              <div className="flex items-center justify-between mb-4">
                <div className="flex items-center">
                  <BookOpen className="h-5 w-5 text-gray-400 mr-3" />
                  <h3 className="text-lg font-medium text-gray-900 dark:text-white">
                    {item.course}
                  </h3>
                </div>
                <div>
                  <Button
                    variant="outline"
                    className="mr-2"
                  >
                    <Edit className="h-5 w-5" />
                  </Button>
                  <Button
                    variant="outline"
                    className="text-red-500 hover:text-red-600"
                  >
                    <Trash className="h-5 w-5" />
                  </Button>
                </div>
              </div>

              <div className="space-y-3">
                {item.prerequisites.map((prereq) => (
                  <div
                    key={prereq.id}
                    className="flex items-center justify-between bg-gray-50 dark:bg-gray-700/50 rounded-lg p-4"
                  >
                    <div className="flex items-center">
                      <ArrowRight className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {prereq.name}
                        </div>
                        <div className="flex items-center mt-1">
                          <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                            prereq.type === 'course'
                              ? 'bg-blue-100 text-blue-800'
                              : 'bg-purple-100 text-purple-800'
                          }`}>
                            {prereq.type}
                          </span>
                          {prereq.required && (
                            <span className="ml-2 px-2 py-1 text-xs font-medium bg-red-100 text-red-800 rounded-full">
                              Required
                            </span>
                          )}
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
};

export default AdminPrerequisites;

/== cms/course-features/AdminReviews.tsx
import React, { useState } from 'react';
import { Search, Star, MessageSquare, ThumbsUp, Filter, Flag } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminReviews = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [selectedFilter, setSelectedFilter] = useState('all');

  const mockReviews = [
    {
      id: '1',
      user: 'Sarah Chen',
      course: 'Python Basics',
      rating: 5,
      review: 'Excellent course! The content was well-structured and easy to follow. The practical examples really helped me understand the concepts better.',
      date: '2024-03-20',
      helpful: 12,
      status: 'approved'
    },
    {
      id: '2',
      user: 'Michael Rodriguez',
      course: 'JavaScript Fundamentals',
      rating: 4,
      review: 'Good course overall. Would appreciate more advanced examples.',
      date: '2024-03-19',
      helpful: 8,
      status: 'pending'
    },
    {
      id: '3',
      user: 'Emma Wilson',
      course: 'React Advanced',
      rating: 5,
      review: 'This course took my React skills to the next level. The instructor explains complex concepts in a very understandable way.',
      date: '2024-03-18',
      helpful: 15,
      status: 'approved'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Course Reviews</h1>
        <div className="flex space-x-4">
          <select
            value={selectedFilter}
            onChange={(e) => setSelectedFilter(e.target.value)}
            className="border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white px-4 py-2"
          >
            <option value="all">All Reviews</option>
            <option value="pending">Pending</option>
            <option value="approved">Approved</option>
            <option value="flagged">Flagged</option>
          </select>
          <Button
            variant="outline"
            className="flex items-center"
          >
            <Filter className="h-5 w-5 mr-2" />
            Filter
          </Button>
        </div>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search reviews..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {mockReviews.map((review) => (
            <div key={review.id} className="p-6">
              <div className="flex items-start justify-between">
                <div className="flex items-start space-x-4">
                  <div className="w-10 h-10 bg-brand-green rounded-full flex items-center justify-center text-white">
                    {review.user.charAt(0)}
                  </div>
                  <div>
                    <div className="flex items-center">
                      <h3 className="font-medium text-gray-900 dark:text-white">{review.user}</h3>
                      <span className="mx-2 text-gray-300 dark:text-gray-600">•</span>
                      <span className="text-sm text-gray-500">{review.date}</span>
                    </div>
                    <div className="text-sm text-gray-500 dark:text-gray-400 mt-1">
                      Course: {review.course}
                    </div>
                    <div className="flex items-center mt-2">
                      {[...Array(5)].map((_, index) => (
                        <Star
                          key={index}
                          className={`h-5 w-5 ${
                            index < review.rating
                              ? 'text-yellow-400 fill-current'
                              : 'text-gray-300 dark:text-gray-600'
                          }`}
                        />
                      ))}
                    </div>
                    <p className="mt-2 text-gray-600 dark:text-gray-300">{review.review}</p>
                    <div className="flex items-center mt-4 space-x-4">
                      <button className="flex items-center text-gray-500 hover:text-gray-700">
                        <ThumbsUp className="h-5 w-5 mr-1" />
                        <span>{review.helpful}</span>
                      </button>
                      <button className="flex items-center text-gray-500 hover:text-gray-700">
                        <MessageSquare className="h-5 w-5  mr-1" />
                        Reply
                      </button>
                      <button className="flex items-center text-red-500 hover:text-red-600">
                        <Flag className="h-5 w-5 mr-1" />
                        Flag
                      </button>
                    </div>
                  </div>
                </div>
                <div className="flex items-center space-x-2">
                  <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                    review.status === 'approved'
                      ? 'bg-green-100 text-green-800'
                      : review.status === 'pending'
                      ? 'bg-yellow-100 text-yellow-800'
                      : 'bg-red-100 text-red-800'
                  }`}>
                    {review.status}
                  </span>
                  <Button
                    variant="outline"
                    className={review.status === 'pending' ? 'text-green-500 hover:text-green-600' : 'text-red-500 hover:text-red-600'}
                  >
                    {review.status === 'pending' ? 'Approve' : 'Remove'}
                  </Button>
                </div>
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
};

export default AdminReviews;

/== cms/integrations/AdminApiManagement.tsx
import React, { useState } from 'react';
import { Search, Plus, Edit, Trash, Key, RefreshCw, Copy, Eye, EyeOff, AlertTriangle } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Modal from '../../../components/atoms/Modal';
import IntegrationForm from '../../../components/molecules/IntegrationForm';

interface ApiKey {
  id: string;
  name: string;
  key: string;
  created: string;
  lastUsed: string;
  requests: number;
  description?: string;
  permissions?: string[];
}

const AdminApiManagement = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [showKeys, setShowKeys] = useState<Record<string, boolean>>({});
  const [isAddModalOpen, setIsAddModalOpen] = useState(false);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
  const [currentApiKey, setCurrentApiKey] = useState<ApiKey | null>(null);
  const [formValues, setFormValues] = useState<Partial<ApiKey>>({});

  const [apiKeys, setApiKeys] = useState<Array<ApiKey>>([{
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
  }]);

  const toggleKeyVisibility = (id: string) => {
    setShowKeys(prev => ({
      ...prev,
      [id]: !prev[id]
    }));
  };

  const copyToClipboard = (text: string) => {
    navigator.clipboard.writeText(text);
    // You could add a toast notification here
  };

  const filteredApiKeys = apiKeys.filter(apiKey => 
    apiKey.name.toLowerCase().includes(searchQuery.toLowerCase())
  );

  const handleOpenAddModal = () => {
    setFormValues({});
    setIsAddModalOpen(true);
  };

  const handleOpenEditModal = (apiKey: ApiKey) => {
    setCurrentApiKey(apiKey);
    setFormValues(apiKey);
    setIsEditModalOpen(true);
  };

  const handleOpenDeleteModal = (apiKey: ApiKey) => {
    setCurrentApiKey(apiKey);
    setIsDeleteModalOpen(true);
  };

  const handleFormChange = (id: string, value: any) => {
    setFormValues(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const generateApiKey = () => {
    // Simulasi pembuatan API key
    const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    let result = '';
    const prefix = formValues.name?.toLowerCase().includes('production') ? 'pk_live_' : 'pk_test_';
    
    for (let i = 0; i < 20; i++) {
      result += characters.charAt(Math.floor(Math.random() * characters.length));
    }
    
    return prefix + result;
  };

  const handleAddApiKey = (e: React.FormEvent) => {
    e.preventDefault();
    const newApiKey: ApiKey = {
      id: Date.now().toString(),
      name: formValues.name || '',
      key: generateApiKey(),
      created: new Date().toISOString().split('T')[0],
      lastUsed: '-',
      requests: 0,
      description: formValues.description,
      permissions: formValues.permissions as string[]
    };

    setApiKeys([...apiKeys, newApiKey]);
    setIsAddModalOpen(false);
    setFormValues({});
  };

  const handleEditApiKey = (e: React.FormEvent) => {
    e.preventDefault();
    if (!currentApiKey) return;

    const updatedApiKeys = apiKeys.map(apiKey => 
      apiKey.id === currentApiKey.id ? { ...apiKey, ...formValues, key: apiKey.key } : apiKey
    );

    setApiKeys(updatedApiKeys);
    setIsEditModalOpen(false);
    setCurrentApiKey(null);
    setFormValues({});
  };

  const handleDeleteApiKey = () => {
    if (!currentApiKey) return;

    const updatedApiKeys = apiKeys.filter(apiKey => apiKey.id !== currentApiKey.id);
    setApiKeys(updatedApiKeys);
    setIsDeleteModalOpen(false);
    setCurrentApiKey(null);
  };

  const regenerateApiKey = (id: string) => {
    setApiKeys(apiKeys.map(apiKey => {
      if (apiKey.id === id) {
        return {
          ...apiKey,
          key: apiKey.key.startsWith('pk_live_') ? 'pk_live_' : 'pk_test_' + Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15)
        };
      }
      return apiKey;
    }));
  };

  const formFields = [
    { id: 'name', label: 'Nama API Key', type: 'text', placeholder: 'Contoh: Production API Key', required: true },
    { id: 'description', label: 'Deskripsi', type: 'textarea', placeholder: 'Deskripsi penggunaan API key ini...' },
    { 
      id: 'permissions', 
      label: 'Permissions', 
      type: 'select', 
      options: [
        { value: 'read', label: 'Read' },
        { value: 'write', label: 'Write' },
        { value: 'delete', label: 'Delete' }
      ]
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">API Management</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
          onClick={handleOpenAddModal}
        >
          <Plus className="h-5 w-5 mr-2" />
          Generate New Key
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search API keys..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Name
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  API Key
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Created
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Used
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Requests
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredApiKeys.map((apiKey) => (
                <tr key={apiKey.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Key className="h-5 w-5 text-gray-400 mr-3" />
                      <div className="font-medium text-gray-900 dark:text-white">
                        {apiKey.name}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center space-x-2">
                      <code className="bg-gray-100 dark:bg-gray-700 px-2 py-1 rounded text-sm font-mono">
                        {showKeys[apiKey.id] ? apiKey.key : '••••••••••••••••'}
                      </code>
                      <button
                        onClick={() => toggleKeyVisibility(apiKey.id)}
                        className="text-gray-500 hover:text-gray-700"
                      >
                        {showKeys[apiKey.id] ? (
                          <EyeOff className="h-4 w-4" />
                        ) : (
                          <Eye className="h-4 w-4" />
                        )}
                      </button>
                      <button
                        onClick={() => copyToClipboard(apiKey.key)}
                        className="text-gray-500 hover:text-gray-700"
                      >
                        <Copy className="h-4 w-4" />
                      </button>
                    </div>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {apiKey.created}
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {apiKey.lastUsed}
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {apiKey.requests.toLocaleString()}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                      title="Regenerate Key"
                      onClick={() => regenerateApiKey(apiKey.id)}
                    >
                      <RefreshCw className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="mr-2"
                      onClick={() => handleOpenEditModal(apiKey)}
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      onClick={() => handleOpenDeleteModal(apiKey)}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Add API Key Modal */}
      <Modal
        isOpen={isAddModalOpen}
        onClose={() => setIsAddModalOpen(false)}
        title="Generate API Key Baru"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleAddApiKey}
          onCancel={() => setIsAddModalOpen(false)}
          submitText="Generate Key"
        />
      </Modal>

      {/* Edit API Key Modal */}
      <Modal
        isOpen={isEditModalOpen}
        onClose={() => setIsEditModalOpen(false)}
        title="Edit API Key"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleEditApiKey}
          onCancel={() => setIsEditModalOpen(false)}
          submitText="Simpan Perubahan"
        />
      </Modal>

      {/* Delete Confirmation Modal */}
      <Modal
        isOpen={isDeleteModalOpen}
        onClose={() => setIsDeleteModalOpen(false)}
        title="Konfirmasi Hapus"
        size="sm"
        footer={
          <>
            <Button 
              variant="outline" 
              onClick={() => setIsDeleteModalOpen(false)}
            >
              Batal
            </Button>
            <Button 
              variant="secondary" 
              className="bg-red-600 hover:bg-red-700"
              onClick={handleDeleteApiKey}
            >
              Hapus
            </Button>
          </>
        }
      >
        <div className="flex items-center">
          <AlertTriangle className="h-12 w-12 text-red-500 mr-4" />
          <div>
            <p className="text-gray-700 dark:text-gray-300 mb-1">
              Apakah Anda yakin ingin menghapus API key <strong>{currentApiKey?.name}</strong>?
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              Tindakan ini tidak dapat dibatalkan dan akan menghapus semua data terkait API key ini.
            </p>
          </div>
        </div>
      </Modal>
    </div>
  );
};

export default AdminApiManagement;

/== cms/integrations/AdminPaymentGateways.tsx
import React, { useState } from 'react';
import { Search, CreditCard, Plus, Edit, Trash, CheckCircle, XCircle, AlertTriangle } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Modal from '../../../components/atoms/Modal';
import IntegrationForm from '../../../components/molecules/IntegrationForm';

interface PaymentGateway {
  id: string;
  name: string;
  status: 'active' | 'inactive';
  mode: 'live' | 'test';
  lastSync: string;
  transactionsCount: number;
  apiKey?: string;
  secretKey?: string;
  webhookUrl?: string;
}

const AdminPaymentGateways = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [isAddModalOpen, setIsAddModalOpen] = useState(false);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
  const [currentGateway, setCurrentGateway] = useState<PaymentGateway | null>(null);
  const [formValues, setFormValues] = useState<Partial<PaymentGateway>>({});

  const [gateways, setGateways] = useState<PaymentGateway[]>([
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
  ]);

  const filteredGateways = gateways.filter(gateway => 
    gateway.name.toLowerCase().includes(searchQuery.toLowerCase())
  );

  const handleOpenAddModal = () => {
    setFormValues({});
    setIsAddModalOpen(true);
  };

  const handleOpenEditModal = (gateway: PaymentGateway) => {
    setCurrentGateway(gateway);
    setFormValues(gateway);
    setIsEditModalOpen(true);
  };

  const handleOpenDeleteModal = (gateway: PaymentGateway) => {
    setCurrentGateway(gateway);
    setIsDeleteModalOpen(true);
  };

  const handleFormChange = (id: string, value: any) => {
    setFormValues(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const handleAddGateway = (e: React.FormEvent) => {
    e.preventDefault();
    const newGateway: PaymentGateway = {
      id: Date.now().toString(),
      name: formValues.name || '',
      status: formValues.status as 'active' | 'inactive' || 'inactive',
      mode: formValues.mode as 'live' | 'test' || 'test',
      lastSync: new Date().toISOString().split('T')[0] + ' ' + new Date().toTimeString().split(' ')[0].substring(0, 5),
      transactionsCount: 0,
      apiKey: formValues.apiKey,
      secretKey: formValues.secretKey,
      webhookUrl: formValues.webhookUrl
    };

    setGateways([...gateways, newGateway]);
    setIsAddModalOpen(false);
    setFormValues({});
  };

  const handleEditGateway = (e: React.FormEvent) => {
    e.preventDefault();
    if (!currentGateway) return;

    const updatedGateways = gateways.map(gateway => 
      gateway.id === currentGateway.id ? { ...gateway, ...formValues } : gateway
    );

    setGateways(updatedGateways);
    setIsEditModalOpen(false);
    setCurrentGateway(null);
    setFormValues({});
  };

  const handleDeleteGateway = () => {
    if (!currentGateway) return;

    const updatedGateways = gateways.filter(gateway => gateway.id !== currentGateway.id);
    setGateways(updatedGateways);
    setIsDeleteModalOpen(false);
    setCurrentGateway(null);
  };

  const formFields = [
    { id: 'name', label: 'Nama Gateway', type: 'text', placeholder: 'Contoh: Stripe, PayPal', required: true },
    { 
      id: 'status', 
      label: 'Status', 
      type: 'select', 
      required: true,
      options: [
        { value: 'active', label: 'Aktif' },
        { value: 'inactive', label: 'Tidak Aktif' }
      ]
    },
    { 
      id: 'mode', 
      label: 'Mode', 
      type: 'select', 
      required: true,
      options: [
        { value: 'test', label: 'Test' },
        { value: 'live', label: 'Live' }
      ]
    },
    { id: 'apiKey', label: 'API Key', type: 'text', placeholder: 'Masukkan API key' },
    { id: 'secretKey', label: 'Secret Key', type: 'password', placeholder: 'Masukkan secret key' },
    { id: 'webhookUrl', label: 'Webhook URL', type: 'text', placeholder: 'https://example.com/webhook' }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Payment Gateways</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
          onClick={handleOpenAddModal}
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Gateway
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search gateways..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Gateway
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Mode
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Sync
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Transactions
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredGateways.map((gateway) => (
                <tr key={gateway.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <CreditCard className="h-5 w-5 text-gray-400 mr-3" />
                      <div className="font-medium text-gray-900 dark:text-white">
                        {gateway.name}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      {gateway.status === 'active' ? (
                        <CheckCircle className="h-5 w-5 text-green-500 mr-2" />
                      ) : (
                        <XCircle className="h-5 w-5 text-red-500 mr-2" />
                      )}
                      <span className={`capitalize ${
                        gateway.status === 'active' ? 'text-green-600' : 'text-red-600'
                      }`}>
                        {gateway.status}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <span className={`px-2 py-1 text-xs font-medium rounded-full ${
                      gateway.mode === 'live'
                        ? 'bg-green-100 text-green-800'
                        : 'bg-yellow-100 text-yellow-800'
                    }`}>
                      {gateway.mode}
                    </span>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {gateway.lastSync}
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {gateway.transactionsCount}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                      onClick={() => handleOpenEditModal(gateway)}
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      onClick={() => handleOpenDeleteModal(gateway)}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Add Gateway Modal */}
      <Modal
        isOpen={isAddModalOpen}
        onClose={() => setIsAddModalOpen(false)}
        title="Tambah Payment Gateway"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleAddGateway}
          onCancel={() => setIsAddModalOpen(false)}
          submitText="Tambah Gateway"
        />
      </Modal>

      {/* Edit Gateway Modal */}
      <Modal
        isOpen={isEditModalOpen}
        onClose={() => setIsEditModalOpen(false)}
        title="Edit Payment Gateway"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleEditGateway}
          onCancel={() => setIsEditModalOpen(false)}
          submitText="Simpan Perubahan"
        />
      </Modal>

      {/* Delete Confirmation Modal */}
      <Modal
        isOpen={isDeleteModalOpen}
        onClose={() => setIsDeleteModalOpen(false)}
        title="Konfirmasi Hapus"
        size="sm"
        footer={
          <>
            <Button 
              variant="outline" 
              onClick={() => setIsDeleteModalOpen(false)}
            >
              Batal
            </Button>
            <Button 
              variant="secondary" 
              className="bg-red-600 hover:bg-red-700"
              onClick={handleDeleteGateway}
            >
              Hapus
            </Button>
          </>
        }
      >
        <div className="flex items-center">
          <AlertTriangle className="h-12 w-12 text-red-500 mr-4" />
          <div>
            <p className="text-gray-700 dark:text-gray-300 mb-1">
              Apakah Anda yakin ingin menghapus gateway <strong>{currentGateway?.name}</strong>?
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              Tindakan ini tidak dapat dibatalkan dan akan menghapus semua data terkait gateway ini.
            </p>
          </div>
        </div>
      </Modal>
    </div>
  );
};

export default AdminPaymentGateways;

/== cms/integrations/AdminSso.tsx
import React, { useState } from 'react';
import { Search, Plus, Edit, Trash, Shield, CheckCircle, XCircle, Key, AlertTriangle } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Modal from '../../../components/atoms/Modal';
import IntegrationForm from '../../../components/molecules/IntegrationForm';

interface SsoProvider {
  id: string;
  name: string;
  domain: string;
  status: 'active' | 'inactive';
  users: number;
  lastSync: string;
  clientId?: string;
  clientSecret?: string;
  callbackUrl?: string;
}

const AdminSso = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [isAddModalOpen, setIsAddModalOpen] = useState(false);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
  const [currentProvider, setCurrentProvider] = useState<SsoProvider | null>(null);
  const [formValues, setFormValues] = useState<Partial<SsoProvider>>({});

  const [providers, setProviders] = useState<SsoProvider[]>([
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
  ]);

  const filteredProviders = providers.filter(provider => 
    provider.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
    provider.domain.toLowerCase().includes(searchQuery.toLowerCase())
  );

  const handleOpenAddModal = () => {
    setFormValues({});
    setIsAddModalOpen(true);
  };

  const handleOpenEditModal = (provider: SsoProvider) => {
    setCurrentProvider(provider);
    setFormValues(provider);
    setIsEditModalOpen(true);
  };

  const handleOpenDeleteModal = (provider: SsoProvider) => {
    setCurrentProvider(provider);
    setIsDeleteModalOpen(true);
  };

  const handleFormChange = (id: string, value: any) => {
    setFormValues(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const handleAddProvider = (e: React.FormEvent) => {
    e.preventDefault();
    const newProvider: SsoProvider = {
      id: Date.now().toString(),
      name: formValues.name || '',
      domain: formValues.domain || '',
      status: formValues.status as 'active' | 'inactive' || 'inactive',
      users: 0,
      lastSync: '-',
      clientId: formValues.clientId,
      clientSecret: formValues.clientSecret,
      callbackUrl: formValues.callbackUrl
    };

    setProviders([...providers, newProvider]);
    setIsAddModalOpen(false);
    setFormValues({});
  };

  const handleEditProvider = (e: React.FormEvent) => {
    e.preventDefault();
    if (!currentProvider) return;

    const updatedProviders = providers.map(provider => 
      provider.id === currentProvider.id ? { ...provider, ...formValues } : provider
    );

    setProviders(updatedProviders);
    setIsEditModalOpen(false);
    setCurrentProvider(null);
    setFormValues({});
  };

  const handleDeleteProvider = () => {
    if (!currentProvider) return;

    const updatedProviders = providers.filter(provider => provider.id !== currentProvider.id);
    setProviders(updatedProviders);
    setIsDeleteModalOpen(false);
    setCurrentProvider(null);
  };

  const toggleProviderStatus = (id: string) => {
    setProviders(providers.map(provider => {
      if (provider.id === id) {
        return {
          ...provider,
          status: provider.status === 'active' ? 'inactive' : 'active'
        };
      }
      return provider;
    }));
  };

  const formFields = [
    { id: 'name', label: 'Nama Provider', type: 'text', placeholder: 'Contoh: Google Workspace', required: true },
    { id: 'domain', label: 'Domain', type: 'text', placeholder: 'example.com', required: true },
    { 
      id: 'status', 
      label: 'Status', 
      type: 'select', 
      required: true,
      options: [
        { value: 'active', label: 'Aktif' },
        { value: 'inactive', label: 'Tidak Aktif' }
      ]
    },
    { id: 'clientId', label: 'Client ID', type: 'text', placeholder: 'Masukkan Client ID' },
    { id: 'clientSecret', label: 'Client Secret', type: 'password', placeholder: 'Masukkan Client Secret' },
    { id: 'callbackUrl', label: 'Callback URL', type: 'text', placeholder: 'https://example.com/auth/callback' }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">SSO Configuration</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
          onClick={handleOpenAddModal}
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Provider
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search SSO providers..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Provider
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Domain
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Users
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Sync
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredProviders.map((provider) => (
                <tr key={provider.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Shield className="h-5 w-5 text-gray-400 mr-3" />
                      <div className="font-medium text-gray-900 dark:text-white">
                        {provider.name}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Key className="h-4 w-4 text-gray-400 mr-2" />
                      <span className="text-gray-500 dark:text-gray-400">
                        {provider.domain}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      {provider.status === 'active' ? (
                        <CheckCircle className="h-5 w-5 text-green-500 mr-2" />
                      ) : (
                        <XCircle className="h-5 w-5 text-red-500 mr-2" />
                      )}
                      <span className={`capitalize ${
                        provider.status === 'active' ? 'text-green-600' : 'text-red-600'
                      }`}>
                        {provider.status}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {provider.users} users
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {provider.lastSync}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                      onClick={() => handleOpenEditModal(provider)}
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      onClick={() => handleOpenDeleteModal(provider)}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Add Provider Modal */}
      <Modal
        isOpen={isAddModalOpen}
        onClose={() => setIsAddModalOpen(false)}
        title="Tambah SSO Provider"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleAddProvider}
          onCancel={() => setIsAddModalOpen(false)}
          submitText="Tambah Provider"
        />
      </Modal>

      {/* Edit Provider Modal */}
      <Modal
        isOpen={isEditModalOpen}
        onClose={() => setIsEditModalOpen(false)}
        title="Edit SSO Provider"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleEditProvider}
          onCancel={() => setIsEditModalOpen(false)}
          submitText="Simpan Perubahan"
        />
      </Modal>

      {/* Delete Confirmation Modal */}
      <Modal
        isOpen={isDeleteModalOpen}
        onClose={() => setIsDeleteModalOpen(false)}
        title="Konfirmasi Hapus"
        size="sm"
        footer={
          <>
            <Button 
              variant="outline" 
              onClick={() => setIsDeleteModalOpen(false)}
            >
              Batal
            </Button>
            <Button 
              variant="secondary" 
              className="bg-red-600 hover:bg-red-700"
              onClick={handleDeleteProvider}
            >
              Hapus
            </Button>
          </>
        }
      >
        <div className="flex items-center">
          <AlertTriangle className="h-12 w-12 text-red-500 mr-4" />
          <div>
            <p className="text-gray-700 dark:text-gray-300 mb-1">
              Apakah Anda yakin ingin menghapus provider <strong>{currentProvider?.name}</strong>?
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              Tindakan ini tidak dapat dibatalkan dan akan menghapus semua data terkait provider ini.
            </p>
          </div>
        </div>
      </Modal>
    </div>
  );
};

export default AdminSso;

/== cms/integrations/AdminThirdParty.tsx
import React, { useState } from 'react';
import { Search, Plus, Edit, Trash, CheckCircle, XCircle, Globe, RefreshCw, AlertTriangle } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Modal from '../../../components/atoms/Modal';
import IntegrationForm from '../../../components/molecules/IntegrationForm';

interface ThirdPartyIntegration {
  id: string;
  name: string;
  category: string;
  status: 'active' | 'inactive';
  lastSync: string;
  apiCalls: number;
  apiKey?: string;
  secretKey?: string;
  webhookUrl?: string;
  settings?: Record<string, any>;
}

const AdminThirdParty = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [isAddModalOpen, setIsAddModalOpen] = useState(false);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
  const [currentIntegration, setCurrentIntegration] = useState<ThirdPartyIntegration | null>(null);
  const [formValues, setFormValues] = useState<Partial<ThirdPartyIntegration>>({});

  const [integrations, setIntegrations] = useState<Array<ThirdPartyIntegration>>([{
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
  }]);

  const filteredIntegrations = integrations.filter(integration => 
    integration.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
    integration.category.toLowerCase().includes(searchQuery.toLowerCase())
  );

  const handleOpenAddModal = () => {
    setFormValues({});
    setIsAddModalOpen(true);
  };

  const handleOpenEditModal = (integration: ThirdPartyIntegration) => {
    setCurrentIntegration(integration);
    setFormValues(integration);
    setIsEditModalOpen(true);
  };

  const handleOpenDeleteModal = (integration: ThirdPartyIntegration) => {
    setCurrentIntegration(integration);
    setIsDeleteModalOpen(true);
  };

  const handleFormChange = (id: string, value: any) => {
    setFormValues(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const handleAddIntegration = (e: React.FormEvent) => {
    e.preventDefault();
    const newIntegration: ThirdPartyIntegration = {
      id: Date.now().toString(),
      name: formValues.name || '',
      category: formValues.category || '',
      status: formValues.status as 'active' | 'inactive' || 'inactive',
      lastSync: '-',
      apiCalls: 0,
      apiKey: formValues.apiKey,
      secretKey: formValues.secretKey,
      webhookUrl: formValues.webhookUrl,
      settings: formValues.settings
    };

    setIntegrations([...integrations, newIntegration]);
    setIsAddModalOpen(false);
    setFormValues({});
  };

  const handleEditIntegration = (e: React.FormEvent) => {
    e.preventDefault();
    if (!currentIntegration) return;

    const updatedIntegrations = integrations.map(integration => 
      integration.id === currentIntegration.id ? { ...integration, ...formValues } : integration
    );

    setIntegrations(updatedIntegrations);
    setIsEditModalOpen(false);
    setCurrentIntegration(null);
    setFormValues({});
  };

  const handleDeleteIntegration = () => {
    if (!currentIntegration) return;

    const updatedIntegrations = integrations.filter(integration => integration.id !== currentIntegration.id);
    setIntegrations(updatedIntegrations);
    setIsDeleteModalOpen(false);
    setCurrentIntegration(null);
  };

  const syncIntegration = (id: string) => {
    setIntegrations(integrations.map(integration => {
      if (integration.id === id) {
        return {
          ...integration,
          lastSync: new Date().toISOString().split('T')[0] + ' ' + new Date().toTimeString().split(' ')[0].substring(0, 5)
        };
      }
      return integration;
    }));
  };

  const formFields = [
    { id: 'name', label: 'Nama Integrasi', type: 'text', placeholder: 'Contoh: Google Analytics', required: true },
    { 
      id: 'category', 
      label: 'Kategori', 
      type: 'select', 
      required: true,
      options: [
        { value: 'Analytics', label: 'Analytics' },
        { value: 'Email', label: 'Email' },
        { value: 'Communication', label: 'Communication' },
        { value: 'CRM', label: 'CRM' },
        { value: 'Marketing', label: 'Marketing' }
      ]
    },
    { 
      id: 'status', 
      label: 'Status', 
      type: 'select', 
      required: true,
      options: [
        { value: 'active', label: 'Aktif' },
        { value: 'inactive', label: 'Tidak Aktif' }
      ]
    },
    { id: 'apiKey', label: 'API Key', type: 'text', placeholder: 'Masukkan API key' },
    { id: 'secretKey', label: 'Secret Key', type: 'password', placeholder: 'Masukkan secret key' },
    { id: 'webhookUrl', label: 'Webhook URL', type: 'text', placeholder: 'https://example.com/webhook' },
    { id: 'settings', label: 'Settings (JSON)', type: 'textarea', placeholder: '{"tracking_id": "UA-XXXXX-Y", "anonymize_ip": true}' }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Third-Party Integrations</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
          onClick={handleOpenAddModal}
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Integration
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search integrations..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Integration
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Category
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Sync
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  API Calls
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredIntegrations.map((integration) => (
                <tr key={integration.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Globe className="h-5 w-5 text-gray-400 mr-3" />
                      <div className="font-medium text-gray-900 dark:text-white">
                        {integration.name}
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <span className="px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded-full">
                      {integration.category}
                    </span>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      {integration.status === 'active' ? (
                        <CheckCircle className="h-5 w-5 text-green-500 mr-2" />
                      ) : (
                        <XCircle className="h-5 w-5 text-red-500 mr-2" />
                      )}
                      <span className={`capitalize ${
                        integration.status === 'active' ? 'text-green-600' : 'text-red-600'
                      }`}>
                        {integration.status}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {integration.lastSync}
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {integration.apiCalls.toLocaleString()}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                      title="Sync Now"
                      onClick={() => syncIntegration(integration.id)}
                    >
                      <RefreshCw className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="mr-2"
                      onClick={() => handleOpenEditModal(integration)}
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      onClick={() => handleOpenDeleteModal(integration)}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Add Integration Modal */}
      <Modal
        isOpen={isAddModalOpen}
        onClose={() => setIsAddModalOpen(false)}
        title="Tambah Integrasi"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleAddIntegration}
          onCancel={() => setIsAddModalOpen(false)}
          submitText="Tambah Integrasi"
        />
      </Modal>

      {/* Edit Integration Modal */}
      <Modal
        isOpen={isEditModalOpen}
        onClose={() => setIsEditModalOpen(false)}
        title="Edit Integrasi"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleEditIntegration}
          onCancel={() => setIsEditModalOpen(false)}
          submitText="Simpan Perubahan"
        />
      </Modal>

      {/* Delete Confirmation Modal */}
      <Modal
        isOpen={isDeleteModalOpen}
        onClose={() => setIsDeleteModalOpen(false)}
        title="Konfirmasi Hapus"
        size="sm"
        footer={
          <>
            <Button 
              variant="outline" 
              onClick={() => setIsDeleteModalOpen(false)}
            >
              Batal
            </Button>
            <Button 
              variant="secondary" 
              className="bg-red-600 hover:bg-red-700"
              onClick={handleDeleteIntegration}
            >
              Hapus
            </Button>
          </>
        }
      >
        <div className="flex items-center">
          <AlertTriangle className="h-12 w-12 text-red-500 mr-4" />
          <div>
            <p className="text-gray-700 dark:text-gray-300 mb-1">
              Apakah Anda yakin ingin menghapus integrasi <strong>{currentIntegration?.name}</strong>?
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              Tindakan ini tidak dapat dibatalkan dan akan menghapus semua data terkait integrasi ini.
            </p>
          </div>
        </div>
      </Modal>
    </div>
  );
};

export default AdminThirdParty;

/== cms/integrations/AdminWebhooks.tsx
import React, { useState } from 'react';
import { Search, Plus, Edit, Trash, Globe, CheckCircle, XCircle, Play, Pause, AlertTriangle } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Modal from '../../../components/atoms/Modal';
import IntegrationForm from '../../../components/molecules/IntegrationForm';

interface Webhook {
  id: string;
  name: string;
  url: string;
  event: string;
  status: 'active' | 'inactive';
  lastTriggered: string;
  successRate: number;
  headers?: Record<string, string>;
  description?: string;
}

const AdminWebhooks = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [isAddModalOpen, setIsAddModalOpen] = useState(false);
  const [isEditModalOpen, setIsEditModalOpen] = useState(false);
  const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
  const [currentWebhook, setCurrentWebhook] = useState<Webhook | null>(null);
  const [formValues, setFormValues] = useState<Partial<Webhook>>({});

  const [webhooks, setWebhooks] = useState<Webhook[]>([
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
  ]);

  const filteredWebhooks = webhooks.filter(webhook => 
    webhook.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
    webhook.event.toLowerCase().includes(searchQuery.toLowerCase())
  );

  const handleOpenAddModal = () => {
    setFormValues({});
    setIsAddModalOpen(true);
  };

  const handleOpenEditModal = (webhook: Webhook) => {
    setCurrentWebhook(webhook);
    setFormValues(webhook);
    setIsEditModalOpen(true);
  };

  const handleOpenDeleteModal = (webhook: Webhook) => {
    setCurrentWebhook(webhook);
    setIsDeleteModalOpen(true);
  };

  const handleFormChange = (id: string, value: any) => {
    setFormValues(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const handleAddWebhook = (e: React.FormEvent) => {
    e.preventDefault();
    const newWebhook: Webhook = {
      id: Date.now().toString(),
      name: formValues.name || '',
      url: formValues.url || '',
      event: formValues.event || '',
      status: formValues.status as 'active' | 'inactive' || 'inactive',
      lastTriggered: '-',
      successRate: 0,
      headers: formValues.headers as Record<string, string>,
      description: formValues.description
    };

    setWebhooks([...webhooks, newWebhook]);
    setIsAddModalOpen(false);
    setFormValues({});
  };

  const handleEditWebhook = (e: React.FormEvent) => {
    e.preventDefault();
    if (!currentWebhook) return;

    const updatedWebhooks = webhooks.map(webhook => 
      webhook.id === currentWebhook.id ? { ...webhook, ...formValues } : webhook
    );

    setWebhooks(updatedWebhooks);
    setIsEditModalOpen(false);
    setCurrentWebhook(null);
    setFormValues({});
  };

  const handleDeleteWebhook = () => {
    if (!currentWebhook) return;

    const updatedWebhooks = webhooks.filter(webhook => webhook.id !== currentWebhook.id);
    setWebhooks(updatedWebhooks);
    setIsDeleteModalOpen(false);
    setCurrentWebhook(null);
  };

  const toggleWebhookStatus = (id: string) => {
    setWebhooks(webhooks.map(webhook => {
      if (webhook.id === id) {
        return {
          ...webhook,
          status: webhook.status === 'active' ? 'inactive' : 'active'
        };
      }
      return webhook;
    }));
  };

  const formFields = [
    { id: 'name', label: 'Nama Webhook', type: 'text', placeholder: 'Contoh: Notifikasi Pembayaran', required: true },
    { id: 'url', label: 'URL Endpoint', type: 'text', placeholder: 'https://example.com/webhook', required: true },
    { 
      id: 'event', 
      label: 'Event', 
      type: 'select', 
      required: true,
      options: [
        { value: 'user.created', label: 'User Created' },
        { value: 'user.updated', label: 'User Updated' },
        { value: 'course.completed', label: 'Course Completed' },
        { value: 'payment.success', label: 'Payment Success' },
        { value: 'payment.failed', label: 'Payment Failed' }
      ]
    },
    { 
      id: 'status', 
      label: 'Status', 
      type: 'select', 
      required: true,
      options: [
        { value: 'active', label: 'Aktif' },
        { value: 'inactive', label: 'Tidak Aktif' }
      ]
    },
    { id: 'description', label: 'Deskripsi', type: 'textarea', placeholder: 'Deskripsi webhook ini...' },
    { id: 'headers', label: 'Headers (JSON)', type: 'textarea', placeholder: '{"Authorization": "Bearer token", "Content-Type": "application/json"}' }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Webhook Configuration</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
          onClick={handleOpenAddModal}
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Webhook
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search webhooks..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Webhook
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Event
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Status
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Last Triggered
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Success Rate
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {filteredWebhooks.map((webhook) => (
                <tr key={webhook.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Globe className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {webhook.name}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {webhook.url}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <span className="px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded-full">
                      {webhook.event}
                    </span>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      {webhook.status === 'active' ? (
                        <CheckCircle className="h-5 w-5 text-green-500 mr-2" />
                      ) : (
                        <XCircle className="h-5 w-5 text-red-500 mr-2" />
                      )}
                      <span className={`capitalize ${
                        webhook.status === 'active' ? 'text-green-600' : 'text-red-600'
                      }`}>
                        {webhook.status}
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 dark:text-gray-400">
                    {webhook.lastTriggered}
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <div className="w-16 bg-gray-200 rounded-full h-2 mr-2">
                        <div
                          className={`h-2 rounded-full ${
                            webhook.successRate >= 98 ? 'bg-green-500' :
                            webhook.successRate >= 90 ? 'bg-yellow-500' : 'bg-red-500'
                          }`}
                          style={{ width: `${webhook.successRate}%` }}
                        />
                      </div>
                      <span className="text-sm text-gray-500 dark:text-gray-400">
                        {webhook.successRate}%
                      </span>
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                      title={webhook.status === 'active' ? 'Pause' : 'Resume'}
                      onClick={() => toggleWebhookStatus(webhook.id)}
                    >
                      {webhook.status === 'active' ? (
                        <Pause className="h-5 w-5" />
                      ) : (
                        <Play className="h-5 w-5" />
                      )}
                    </Button>
                    <Button
                      variant="outline"
                      className="mr-2"
                      onClick={() => handleOpenEditModal(webhook)}
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      onClick={() => handleOpenDeleteModal(webhook)}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Add Webhook Modal */}
      <Modal
        isOpen={isAddModalOpen}
        onClose={() => setIsAddModalOpen(false)}
        title="Tambah Webhook"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleAddWebhook}
          onCancel={() => setIsAddModalOpen(false)}
          submitText="Tambah Webhook"
        />
      </Modal>

      {/* Edit Webhook Modal */}
      <Modal
        isOpen={isEditModalOpen}
        onClose={() => setIsEditModalOpen(false)}
        title="Edit Webhook"
      >
        <IntegrationForm
          fields={formFields}
          values={formValues}
          onChange={handleFormChange}
          onSubmit={handleEditWebhook}
          onCancel={() => setIsEditModalOpen(false)}
          submitText="Simpan Perubahan"
        />
      </Modal>

      {/* Delete Confirmation Modal */}
      <Modal
        isOpen={isDeleteModalOpen}
        onClose={() => setIsDeleteModalOpen(false)}
        title="Konfirmasi Hapus"
        size="sm"
        footer={
          <>
            <Button 
              variant="outline" 
              onClick={() => setIsDeleteModalOpen(false)}
            >
              Batal
            </Button>
            <Button 
              variant="secondary" 
              className="bg-red-600 hover:bg-red-700"
              onClick={handleDeleteWebhook}
            >
              Hapus
            </Button>
          </>
        }
      >
        <div className="flex items-center">
          <AlertTriangle className="h-12 w-12 text-red-500 mr-4" />
          <div>
            <p className="text-gray-700 dark:text-gray-300 mb-1">
              Apakah Anda yakin ingin menghapus webhook <strong>{currentWebhook?.name}</strong>?
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              Tindakan ini tidak dapat dibatalkan dan akan menghapus semua data terkait webhook ini.
            </p>
          </div>
        </div>
      </Modal>
    </div>
  );
};

export default AdminWebhooks;

/== cms/integrations/IntegrationsIndex.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Server, CreditCard, Shield, Globe, Webhook } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Heading from '../../../components/atoms/Heading';
import Text from '../../../components/atoms/Text';

interface IntegrationCardProps {
  title: string;
  description: string;
  icon: React.ReactNode;
  path: string;
}

const IntegrationCard: React.FC<IntegrationCardProps> = ({ title, description, icon, path }) => {
  return (
    <Link to={path} className="block">
      <Card className="h-full p-6 transition-all hover:shadow-lg hover:border-brand-green">
        <div className="flex items-start">
          <div className="p-3 rounded-full bg-brand-green/10 text-brand-green mr-4">
            {icon}
          </div>
          <div>
            <Heading level="h3" className="text-lg font-semibold mb-2 text-gray-200 text-white">{title}</Heading>
            <Text className="text-gray-600 dark:text-white">{description}</Text>
          </div>
        </div>
      </Card>
    </Link>
  );
};

const IntegrationsIndex = () => {
  const integrations = [
    {
      title: 'Manajemen API',
      description: 'Kelola API keys, rate limits, dan akses API untuk aplikasi eksternal.',
      icon: <Server className="h-6 w-6" />,
      path: '/cms/integrations/api-management'
    },
    {
      title: 'Gateway Pembayaran',
      description: 'Konfigurasi dan kelola gateway pembayaran seperti Stripe, PayPal, dan lainnya.',
      icon: <CreditCard className="h-6 w-6" />,
      path: '/cms/integrations/payment-gateways'
    },
    {
      title: 'SSO (Single Sign-On)',
      description: 'Atur integrasi dengan penyedia identitas untuk autentikasi terpusat.',
      icon: <Shield className="h-6 w-6" />,
      path: '/cms/integrations/sso'
    },
    {
      title: 'Integrasi Pihak Ketiga',
      description: 'Hubungkan dengan layanan pihak ketiga seperti Google Analytics, Mailchimp, dan lainnya.',
      icon: <Globe className="h-6 w-6" />,
      path: '/cms/integrations/third-party'
    },
    {
      title: 'Webhook',
      description: 'Konfigurasi webhook untuk mengirim notifikasi ke sistem eksternal saat terjadi peristiwa tertentu.',
      icon: <Webhook className="h-6 w-6" />,
      path: '/cms/integrations/webhooks'
    }
  ];

  return (
    <div>
      <div className="mb-8">
        <Heading level="h1" className="text-2xl font-bold mb-2">Integrasi</Heading>
        <Text className="text-gray-600 dark:text-white">
          Kelola semua integrasi sistem dengan layanan dan aplikasi eksternal.
        </Text>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        {integrations.map((integration, index) => (
          <IntegrationCard
            key={index}
            title={integration.title}
            description={integration.description}
            icon={integration.icon}
            path={integration.path}
          />
        ))}
      </div>
    </div>
  );
};

export default IntegrationsIndex;

/== cms/monetization/affiliates.tsx
import React, { useState, useEffect } from "react";
import Card from "../../../components/atoms/Card";
import Heading from "../../../components/atoms/Heading";
import Button from "../../../components/atoms/Button";
import Text from "../../../components/atoms/Text";
import TabGroup from "../../../components/molecules/TabGroup";
import AffiliateTable from "../../../components/molecules/AffiliateTable";
import AffiliateForm from "../../../components/organisms/AffiliateForm";
import AffiliateAnalytics from "../../../components/organisms/AffiliateAnalytics";
import AffiliatePromotionMaterials from "../../../components/organisms/AffiliatePromotionMaterials";
import AffiliatorList from "../../../components/organisms/AffiliatorList";
import AffiliateSettings from "../../../components/organisms/AffiliateSettings";
import Modal from "../../../components/atoms/Modal";
import IconButton from "../../../components/atoms/IconButton";
import { Plus } from "lucide-react";
import { useAffiliateStore } from "../../../store/affiliateStore";

const AffiliatesManagement: React.FC = () => {
  // Get data from store
  const { 
    programs, 
    affiliators, 
    promotionalMaterials, 
    settings, 
    analytics,
    addProgram, 
    updateProgram, 
    deleteProgram,
    updateAffiliator,
    deleteAffiliator,
    approveAffiliator,
    rejectAffiliator,
    addPromotionalMaterial,
    updatePromotionalMaterial,
    deletePromotionalMaterial,
    updateSettings,
    refreshAnalytics
  } = useAffiliateStore();

  const [activeTab, setActiveTab] = useState("programs");
  const [showFormModal, setShowFormModal] = useState(false);
  const [currentProgramId, setCurrentProgramId] = useState<string | null>(null);
  const [showConfirmDelete, setShowConfirmDelete] = useState(false);
  const [deleteId, setDeleteId] = useState<string | null>(null);
  const [deleteType, setDeleteType] = useState<"program" | "affiliator">("program");

  // Refresh analytics on load
  useEffect(() => {
    refreshAnalytics();
  }, [refreshAnalytics]);

  const handleAddProgram = (data: any) => {
    console.log("Form data received:", data);
    
    try {
      addProgram({
        name: data.name,
        referralCode: data.referralCode,
        commission: data.commission ? `${data.commission}%` : "0%",
        cookieDuration: Number(data.cookieDuration) || 30,
        description: data.description || "",
        terms: data.terms || "",
        status: data.status || "active",
        paymentSchedule: data.paymentSchedule || "monthly",
        multilevel: Boolean(data.multilevel),
        level2Commission: data.multilevel && data.level2Commission ? `${data.level2Commission}%` : "",
        minPayoutAmount: settings.globalSettings.minPayoutAmount,
      });
      console.log("Program added successfully");
      setShowFormModal(false);
    } catch (error) {
      console.error("Error adding program:", error);
      // You could add an error state here and display it to the user
    }
  };

  const handleEditProgram = (data: any) => {
    if (currentProgramId) {
      updateProgram(currentProgramId, {
        name: data.name,
        referralCode: data.referralCode,
        commission: `${data.commission}%`,
        cookieDuration: data.cookieDuration,
        description: data.description || "",
        terms: data.terms || "",
        status: data.status as any,
        paymentSchedule: data.paymentSchedule as any,
        multilevel: data.multilevel,
        level2Commission: data.level2Commission || "",
      });
      setShowFormModal(false);
      setCurrentProgramId(null);
    }
  };

  const handleEditClick = (id: string) => {
    setCurrentProgramId(id);
    setShowFormModal(true);
  };

  const handleDeleteClick = (id: string, type: "program" | "affiliator" = "program") => {
    setDeleteId(id);
    setDeleteType(type);
    setShowConfirmDelete(true);
  };

  const confirmDelete = () => {
    if (deleteId) {
      if (deleteType === "program") {
        deleteProgram(deleteId);
      } else {
        deleteAffiliator(deleteId);
      }
      setShowConfirmDelete(false);
      setDeleteId(null);
    }
  };

  const handleViewClick = (id: string) => {
    // Implement view logic here
    console.log("View program with ID:", id);
  };

  const handleAddMaterial = (material: any) => {
    addPromotionalMaterial({
      programId: material.programId || "1", // Default to first program if not specified
      name: material.name,
      type: material.type as any,
      preview: material.preview,
      description: material.description,
    });
  };

  const handleEditMaterial = (id: string, material: any) => {
    updatePromotionalMaterial(id, {
      name: material.name,
      type: material.type as any,
      preview: material.preview,
      description: material.description,
    });
  };

  const handleDeleteMaterial = (id: string) => {
    deletePromotionalMaterial(id);
  };

  const handleViewAffiliator = (id: string) => {
    console.log("View affiliator with ID:", id);
  };

  const handleEditAffiliator = (id: string) => {
    console.log("Edit affiliator with ID:", id);
  };

  const handleApproveAffiliator = (id: string) => {
    approveAffiliator(id);
  };

  const handleRejectAffiliator = (id: string) => {
    rejectAffiliator(id);
  };

  const handleDeleteAffiliator = (id: string) => {
    handleDeleteClick(id, "affiliator");
  };

  const handleUpdateSettings = (newSettings: any) => {
    updateSettings(newSettings);
  };

  const getCurrentProgram = () => {
    if (currentProgramId) {
      const program = programs.find(
        (program) => program.id === currentProgramId
      );
      if (program) {
        return {
          name: program.name,
          referralCode: program.referralCode,
          commission: program.commission.replace("%", ""),
          cookieDuration: program.cookieDuration,
          description: program.description,
          terms: program.terms,
          status: program.status,
          paymentSchedule: program.paymentSchedule,
          multilevel: program.multilevel,
          level2Commission: program.level2Commission.replace("%", ""),
        };
      }
    }
    return undefined;
  };

  const tabs = [
    { id: "programs", label: "Program Afiliasi" },
    { id: "analytics", label: "Analitik" },
    { id: "materials", label: "Materi Promosi" },
    { id: "settings", label: "Pengaturan" },
    { id: "affiliators", label: "Daftar Afiliator" },
  ];

  return (
    <div className="p-6 bg-white dark:bg-gray-900">
      <div className="flex justify-between items-center mb-6">
        <div>
          <Heading level={2} className="text-gray-900 dark:text-white">Afiliasi & Referral</Heading>
          <Text className="text-gray-500 dark:text-gray-400 mt-1">
            Kelola program afiliasi dan referral untuk meningkatkan jangkauan
          </Text>
        </div>
        {activeTab === "programs" && (
          <Button
            variant="primary"
            onClick={() => {
              setCurrentProgramId(null);
              setShowFormModal(true);
            }}
          >
            <Plus className="mr-2 h-5 w-5" /> Tambah Program Afiliasi
          </Button>
        )}
      </div>

      <Card className="mb-6">
        <TabGroup
          tabs={tabs}
          activeTab={activeTab}
          onChange={(id) => setActiveTab(id)}
        />
      </Card>

      {activeTab === "programs" && (
        <Card>
          <div className="p-4">
            <AffiliateTable
              affiliates={programs}
              onEdit={handleEditClick}
              onView={handleViewClick}
              onDelete={(id) => handleDeleteClick(id, "program")}
            />
          </div>
        </Card>
      )}

      {activeTab === "analytics" && (
        <AffiliateAnalytics data={analytics} />
      )}

      {activeTab === "materials" && (
        <Card className="p-4">
          <AffiliatePromotionMaterials
            materials={promotionalMaterials}
            onAdd={handleAddMaterial}
            onEdit={handleEditMaterial}
            onDelete={handleDeleteMaterial}
          />
        </Card>
      )}

      {activeTab === "settings" && (
        <AffiliateSettings 
          settings={settings}
          onSave={handleUpdateSettings}
        />
      )}

      {activeTab === "affiliators" && (
        <Card className="p-4">
          <AffiliatorList 
            affiliators={affiliators}
            onViewAffiliator={handleViewAffiliator}
            onEditAffiliator={handleEditAffiliator}
            onDeleteAffiliator={handleDeleteAffiliator}
            onApproveAffiliator={handleApproveAffiliator}
            onRejectAffiliator={handleRejectAffiliator}
          />
        </Card>
      )}

      {/* Add/Edit Program Modal */}
      <Modal
        open={showFormModal}
        onClose={() => setShowFormModal(false)}
        title={currentProgramId ? "Edit Program Afiliasi" : "Tambah Program Afiliasi"}
        size="lg"
      >
        <AffiliateForm
          initialData={getCurrentProgram()}
          onSubmit={currentProgramId ? handleEditProgram : handleAddProgram}
          onCancel={() => setShowFormModal(false)}
        />
      </Modal>

      {/* Confirm Delete Modal */}
      <Modal
        open={showConfirmDelete}
        onClose={() => setShowConfirmDelete(false)}
        title={`Konfirmasi Hapus ${deleteType === "program" ? "Program" : "Afiliator"}`}
        size="sm"
        footer={
          <div className="flex justify-end space-x-2">
            <Button variant="outline" onClick={() => setShowConfirmDelete(false)}>
              Batal
            </Button>
            <Button variant="danger" onClick={confirmDelete}>
              Hapus
            </Button>
          </div>
        }
      >
        <Text className="text-gray-700 dark:text-gray-300">
          {deleteType === "program" 
            ? "Apakah Anda yakin ingin menghapus program afiliasi ini? Tindakan ini tidak dapat dibatalkan."
            : "Apakah Anda yakin ingin menghapus afiliator ini? Semua data referral dan komisi juga akan dihapus. Tindakan ini tidak dapat dibatalkan."}
        </Text>
      </Modal>
    </div>
  );
};

export default AffiliatesManagement;

/== cms/monetization/bundles.tsx
import React, { useEffect, useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { DashboardBundleLayout } from '../../../components/templates/DashboardBundleLayout';
import { BundlesList } from '../../../components/organisms/BundlesList';
import { BundleForm } from '../../../components/organisms/BundleForm';
import { BundleStatistics } from '../../../components/organisms/BundleStatistics';
import  Button  from '../../../components/atoms/Button';
import useBundleStore, { Bundle } from '../../../store/useBundleStore';
import BundlesManagement from '../../../classes/BundlesManagement';

enum View {
  LIST = 'list',
  CREATE = 'create',
  EDIT = 'edit',
  DETAILS = 'details',
}

const BundlesPage: React.FC = () => {
  const navigate = useNavigate();
  const [currentView, setCurrentView] = useState<View>(View.LIST);
  const { bundles, courses, selectedBundle, stats, isLoading, error, fetchBundles, fetchCourses, fetchBundleStats } = useBundleStore();
  const bundlesManager = BundlesManagement.getInstance();

  useEffect(() => {
    fetchBundles();
    fetchCourses();
  }, [fetchBundles, fetchCourses]);

  const handleCreateBundle = async (bundle: Omit<Bundle, 'id' | 'createdAt' | 'updatedAt'>) => {
    await bundlesManager.createBundle(bundle);
    setCurrentView(View.LIST);
  };

  const handleUpdateBundle = async (id: string, bundle: Partial<Bundle>) => {
    await bundlesManager.updateBundle(id, bundle);
    setCurrentView(View.LIST);
  };

  const handleDeleteBundle = async (id: string) => {
    if (window.confirm('Are you sure you want to delete this bundle?')) {
      await bundlesManager.deleteBundle(id);
    }
  };

  const handleViewBundle = (id: string) => {
    bundlesManager.selectBundle(id);
    fetchBundleStats(id);
    setCurrentView(View.DETAILS);
  };

  const handleEditBundle = (id: string) => {
    bundlesManager.selectBundle(id);
    setCurrentView(View.EDIT);
  };

  const renderView = () => {
    switch (currentView) {
      case View.CREATE:
        return (
          <BundleForm
            courses={courses}
            onSubmit={handleCreateBundle}
            isLoading={isLoading}
          />
        );
      case View.EDIT:
        return selectedBundle ? (
          <BundleForm
            courses={courses}
            initialData={selectedBundle}
            onSubmit={(bundle) => handleUpdateBundle(selectedBundle.id, bundle)}
            isLoading={isLoading}
          />
        ) : (
          <div>Loading...</div>
        );
      case View.DETAILS:
        return selectedBundle ? (
          <div className="space-y-6">
            <div className="flex justify-between items-center">
              <h2 className="text-xl font-semibold text-gray-900">{selectedBundle.title}</h2>
              <Button variant="primary" onClick={() => handleEditBundle(selectedBundle.id)}>
                Edit Bundle
              </Button>
            </div>
            
            <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
              <div className="md:col-span-2">
                <div className="bg-gray-800 rounded-lg shadow-sm border p-4 mb-6">
                  <div className="mb-4">
                    <h3 className="text-lg font-medium text-gray-900 mb-2">Bundle Details</h3>
                    <p className="text-gray-600">{selectedBundle.description}</p>
                  </div>
                  
                  <div className="grid grid-cols-2 gap-4">
                    <div>
                      <div className="text-sm text-gray-500">Original Price</div>
                      <div className="text-lg font-medium line-through">${selectedBundle.originalPrice.toFixed(2)}</div>
                    </div>
                    <div>
                      <div className="text-sm text-gray-500">Bundle Price</div>
                      <div className="text-lg font-medium text-blue-600">${selectedBundle.bundlePrice.toFixed(2)}</div>
                    </div>
                    <div>
                      <div className="text-sm text-gray-500">Discount</div>
                      <div className="text-lg font-medium">{selectedBundle.discountPercentage}%</div>
                    </div>
                    <div>
                      <div className="text-sm text-gray-500">Access Duration</div>
                      <div className="text-lg font-medium">
                        {selectedBundle.accessDuration === 'lifetime' 
                          ? 'Lifetime' 
                          : `${selectedBundle.accessDuration} days`}
                      </div>
                    </div>
                  </div>
                </div>
                
                <div className="bg-white dark:bg-dark-800 dark:bg-dark-800 rounded-lg shadow-sm border p-4">
                  <h3 className="text-lg font-medium text-gray-900 mb-4">Courses in Bundle</h3>
                  <ul className="divide-y divide-gray-200">
                    {selectedBundle.courses.map((courseId) => {
                      const course = courses.find(c => c.id === courseId);
                      return course ? (
                        <li key={course.id} className="py-3 flex justify-between items-center">
                          <div>
                            <div className="font-medium">{course.title}</div>
                            <div className="text-sm text-gray-500">{course.level} · {course.duration} hours</div>
                          </div>
                          <div className="text-gray-600">${course.price.toFixed(2)}</div>
                        </li>
                      ) : null;
                    })}
                  </ul>
                </div>
              </div>
              
              <div>
                <BundleStatistics
                  stats={stats.find(s => s.bundleId === selectedBundle.id) || {
                    id: '',
                    bundleId: selectedBundle.id,
                    purchases: 0,
                    completionRate: 0,
                    averageCompletionTime: 0,
                    mostPopularCourse: '',
                    revenue: 0,
                    createdAt: new Date(),
                  }}
                  isLoading={isLoading}
                />
              </div>
            </div>
          </div>
        ) : (
          <div>Loading...</div>
        );
      case View.LIST:
      default:
        return (
          <BundlesList
            bundles={bundles}
            onEdit={handleEditBundle}
            onDelete={handleDeleteBundle}
            onView={handleViewBundle}
            isLoading={isLoading}
          />
        );
    }
  };

  const renderActions = () => {
    switch (currentView) {
      case View.LIST:
        return (
          <Button onClick={() => setCurrentView(View.CREATE)}>
            Create Bundle
          </Button>
        );
      case View.CREATE:
      case View.EDIT:
        return (
          <Button variant="outline" onClick={() => setCurrentView(View.LIST)}>
            Cancel
          </Button>
        );
      case View.DETAILS:
        return (
          <Button variant="outline" onClick={() => setCurrentView(View.LIST)}>
            Back to List
          </Button>
        );
      default:
        return null;
    }
  };

  return (
    <DashboardBundleLayout
      title={
        currentView === View.CREATE
          ? 'Create Bundle'
          : currentView === View.EDIT
          ? 'Edit Bundle'
          : currentView === View.DETAILS
          ? 'Bundle Details'
          : 'Course Bundles'
      }
      actions={renderActions()}
    >
      {error && (
        <div className="bg-red-50 border-l-4 border-red-400 p-4 mb-6">
          <div className="flex">
            <div className="flex-shrink-0">
              <svg className="h-5 w-5 text-red-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clipRule="evenodd" />
              </svg>
            </div>
            <div className="ml-3">
              <p className="text-sm text-red-700">{error}</p>
            </div>
          </div>
        </div>
      )}
      {renderView()}
    </DashboardBundleLayout>
  );
};

export default BundlesPage;

/== cms/monetization/coupons.tsx
import React, { useState } from "react";
import Button from "../../../components/atoms/Button";
import Card from "../../../components/atoms/Card";
import Heading from "../../../components/atoms/Heading";
import Input from "../../../components/atoms/Input";
import Modal from "../../../components/atoms/Modal";

const CouponsManagement: React.FC = () => {
  // State untuk mengelola kupon
  const [coupons, setCoupons] = useState([
    {
      id: 1,
      code: "HEMAT50",
      discountType: "percentage",
      discountValue: 50,
      validFrom: "2024-01-01",
      validUntil: "2024-12-31",
      usageLimit: 100,
      usageCount: 45,
      userLimit: 1,
      targetUser: "all",
      targetProduct: "all",
      minimumPurchase: 100000,
      status: "active"
    },
    {
      id: 2,
      code: "WELCOME10",
      discountType: "percentage",
      discountValue: 10,
      validFrom: "2024-01-01",
      validUntil: "2024-06-30",
      usageLimit: 500,
      usageCount: 123,
      userLimit: 1,
      targetUser: "new",
      targetProduct: "all",
      minimumPurchase: 0,
      status: "active"
    },
    {
      id: 3,
      code: "FLAT50K",
      discountType: "fixed",
      discountValue: 50000,
      validFrom: "2024-03-01",
      validUntil: "2024-05-31",
      usageLimit: 200,
      usageCount: 87,
      userLimit: 1,
      targetUser: "all",
      targetProduct: "premium",
      minimumPurchase: 200000,
      status: "active"
    }
  ]);

  // State untuk modal
  const [showModal, setShowModal] = useState(false);
  const [editingCoupon, setEditingCoupon] = useState<any>(null);
  const [formData, setFormData] = useState({
    code: "",
    discountType: "percentage",
    discountValue: 0,
    validFrom: "",
    validUntil: "",
    usageLimit: 0,
    userLimit: 1,
    targetUser: "all",
    targetProduct: "all",
    minimumPurchase: 0,
    status: "active"
  });

  // Handler untuk perubahan form
  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: name === "discountValue" || name === "usageLimit" || name === "userLimit" || name === "minimumPurchase" 
        ? parseInt(value) || 0 
        : value
    }));
  };

  // Handler untuk membuka modal tambah kupon
  const handleAddCoupon = () => {
    setEditingCoupon(null);
    setFormData({
      code: "",
      discountType: "percentage",
      discountValue: 0,
      validFrom: new Date().toISOString().split('T')[0],
      validUntil: "",
      usageLimit: 0,
      userLimit: 1,
      targetUser: "all",
      targetProduct: "all",
      minimumPurchase: 0,
      status: "active"
    });
    setShowModal(true);
  };

  // Handler untuk membuka modal edit kupon
  const handleEditCoupon = (coupon: any) => {
    setEditingCoupon(coupon);
    setFormData({
      ...coupon
    });
    setShowModal(true);
  };

  // Handler untuk menyimpan kupon
  const handleSaveCoupon = () => {
    if (editingCoupon) {
      // Update kupon yang sudah ada
      setCoupons(coupons.map(c => c.id === editingCoupon.id ? { ...formData, id: editingCoupon.id } : c));
    } else {
      // Tambah kupon baru
      setCoupons([...coupons, { ...formData, id: Date.now(), usageCount: 0 }]);
    }
    setShowModal(false);
  };

  // Handler untuk menghapus kupon
  const handleDeleteCoupon = (id: number) => {
    if (window.confirm("Apakah Anda yakin ingin menghapus kupon ini?")) {
      setCoupons(coupons.filter(c => c.id !== id));
    }
  };

  // Format tanggal untuk tampilan
  const formatDate = (dateString: string) => {
    const date = new Date(dateString);
    return date.toLocaleDateString('id-ID', { day: '2-digit', month: '2-digit', year: 'numeric' });
  };

  // Format diskon untuk tampilan
  const formatDiscount = (type: string, value: number) => {
    return type === "percentage" ? `${value}%` : `Rp ${value.toLocaleString('id-ID')}`;
  };

  return (
    <div className="p-6">
      <div className="flex justify-between items-center mb-6">
        <Heading level={2}>Diskon & Kupon</Heading>
        <Button 
          onClick={handleAddCoupon} 
          variant="primary"
          className="flex items-center"
        >
          Tambah Kupon
        </Button>
      </div>

      {/* Statistik Kupon */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
        <Card className="p-4">
          <div className="text-sm text-gray-500 mb-1">Total Kupon Aktif</div>
          <div className="text-2xl font-bold">{coupons.filter(c => c.status === "active").length}</div>
        </Card>
        <Card className="p-4">
          <div className="text-sm text-gray-500 mb-1">Total Penggunaan</div>
          <div className="text-2xl font-bold">{coupons.reduce((sum, c) => sum + c.usageCount, 0)}</div>
        </Card>
        <Card className="p-4">
          <div className="text-sm text-gray-500 mb-1">Kupon Akan Berakhir</div>
          <div className="text-2xl font-bold">
            {coupons.filter(c => {
              const now = new Date();
              const expiry = new Date(c.validUntil);
              const diffDays = Math.ceil((expiry.getTime() - now.getTime()) / (1000 * 60 * 60 * 24));
              return diffDays <= 30 && diffDays > 0 && c.status === "active";
            }).length}
          </div>
        </Card>
      </div>

      {/* Tabel Kupon */}
      <Card className="overflow-hidden">
        <div className="overflow-x-auto">
          <table className="min-w-full divide-y divide-gray-200">
            <thead className="bg-gray-600 text-white">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Kode Kupon</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Diskon</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Periode</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Penggunaan</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Target</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-200 uppercase tracking-wider">Status</th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-200 uppercase tracking-wider">Aksi</th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200">
              {coupons.map((coupon) => (
                <tr key={coupon.id} className="hover:bg-gray-800">
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div className="font-medium text-gray-900 dark:text-white">{coupon.code}</div>
                    {coupon.minimumPurchase > 0 && (
                      <div className="text-xs text-gray-500">Min. Rp {coupon.minimumPurchase.toLocaleString('id-ID')}</div>
                    )}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className="px-2 py-1 text-xs font-medium rounded-full bg-blue-100 text-blue-800">
                      {formatDiscount(coupon.discountType, coupon.discountValue)}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div>{formatDate(coupon.validFrom)} - {formatDate(coupon.validUntil)}</div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div>{coupon.usageCount} / {coupon.usageLimit || "∞"}</div>
                    <div className="text-xs text-gray-500">Max {coupon.userLimit || "∞"}/pengguna</div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <div>
                      {coupon.targetUser === "all" ? "Semua Pengguna" : "Pengguna Baru"}
                    </div>
                    <div className="text-xs text-gray-500">
                      {coupon.targetProduct === "all" ? "Semua Produk" : "Produk Premium"}
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <span className={`px-2 py-1 text-xs font-medium rounded-full ${coupon.status === "active" ? "bg-green-100 text-green-800" : "bg-gray-100 text-gray-800"}`}>
                      {coupon.status === "active" ? "Aktif" : "Tidak Aktif"}
                    </span>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button 
                      onClick={() => handleEditCoupon(coupon)} 
                      variant="outline" 
                      size="sm"
                      className="mr-2"
                    >
                      Edit
                    </Button>
                    <Button 
                      onClick={() => handleDeleteCoupon(coupon.id)} 
                      variant="outline" 
                      size="sm"
                      className="text-red-600 border-red-300 hover:bg-red-50"
                    >
                      Hapus
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      {/* Modal Form */}
      <Modal 
        isOpen={showModal} 
        onClose={() => setShowModal(false)}
        title={editingCoupon ? "Edit Kupon" : "Tambah Kupon"}
        size="lg"
      >
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Input 
            label="Kode Kupon" 
            name="code"
            value={formData.code} 
            onChange={handleChange}
            placeholder="Contoh: HEMAT50" 
          />
          
          <div className="w-full">
            <label className="block text-sm font-medium text-gray-700 mb-1">Tipe Diskon</label>
            <select 
              name="discountType" 
              value={formData.discountType}
              onChange={handleChange}
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="percentage">Persentase (%)</option>
              <option value="fixed">Nominal Tetap (Rp)</option>
            </select>
          </div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Input 
            label={formData.discountType === "percentage" ? "Nilai Diskon (%)" : "Nilai Diskon (Rp)"} 
            name="discountValue"
            type="number" 
            value={formData.discountValue} 
            onChange={handleChange}
            placeholder={formData.discountType === "percentage" ? "Contoh: 50" : "Contoh: 50000"} 
          />
          
          <Input 
            label="Minimum Pembelian (Rp)" 
            name="minimumPurchase"
            type="number" 
            value={formData.minimumPurchase} 
            onChange={handleChange}
            placeholder="Contoh: 100000" 
          />
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Input 
            label="Berlaku Dari" 
            name="validFrom"
            type="date" 
            value={formData.validFrom} 
            onChange={handleChange}
          />
          
          <Input 
            label="Berlaku Sampai" 
            name="validUntil"
            type="date" 
            value={formData.validUntil} 
            onChange={handleChange}
          />
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <Input 
            label="Batas Penggunaan Total" 
            name="usageLimit"
            type="number" 
            value={formData.usageLimit} 
            onChange={handleChange}
            placeholder="0 = tidak terbatas" 
          />
          
          <Input 
            label="Batas Per Pengguna" 
            name="userLimit"
            type="number" 
            value={formData.userLimit} 
            onChange={handleChange}
            placeholder="0 = tidak terbatas" 
          />
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <div className="w-full">
            <label className="block text-sm font-medium text-gray-700 mb-1">Target Pengguna</label>
            <select 
              name="targetUser" 
              value={formData.targetUser}
              onChange={handleChange}
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="all">Semua Pengguna</option>
              <option value="new">Pengguna Baru</option>
            </select>
          </div>
          
          <div className="w-full">
            <label className="block text-sm font-medium text-gray-700 mb-1">Target Produk</label>
            <select 
              name="targetProduct" 
              value={formData.targetProduct}
              onChange={handleChange}
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="all">Semua Produk</option>
              <option value="premium">Produk Premium</option>
            </select>
          </div>
        </div>

        <div className="mb-4">
          <label className="block text-sm font-medium text-gray-700 mb-1">Status</label>
          <select 
            name="status" 
            value={formData.status}
            onChange={handleChange}
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <option value="active">Aktif</option>
            <option value="inactive">Tidak Aktif</option>
          </select>
        </div>

        <div className="flex justify-end gap-2 mt-6">
          <Button 
            onClick={() => setShowModal(false)} 
            variant="outline"
          >
            Batal
          </Button>
          <Button 
            onClick={handleSaveCoupon} 
            variant="primary"
          >
            Simpan
          </Button>
        </div>
      </Modal>
    </div>
  );
};

export default CouponsManagement;

/== cms/monetization/index.tsx
import React from "react";
import { useNavigate } from "react-router-dom";
import { CreditCard, Tag, Gift, Package, Users, BarChart } from "lucide-react";
import Card from "../../../components/atoms/Card";

const MonetizationIndex: React.FC = () => {
  const navigate = useNavigate();
  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">Monetisasi & Komersial</h1>
      </div>
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/subscription")}>
          <div className="flex items-center mb-4">
            <CreditCard className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Manajemen Langganan</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Kelola model langganan dan paket berlangganan.</p>
        </Card>
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/coupons")}>
          <div className="flex items-center mb-4">
            <Tag className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Diskon & Kupon</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Buat dan kelola promosi, diskon, serta kupon.</p>
        </Card>
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/pricing")}>
          <div className="flex items-center mb-4">
            <Gift className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Manajemen Paket Harga</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Kelola paket harga dan fitur layanan.</p>
        </Card>
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/bundles")}>
          <div className="flex items-center mb-4">
            <Package className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Bundling Kursus</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Buat dan jual paket kursus.</p>
        </Card>
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/affiliates")}>
          <div className="flex items-center mb-4">
            <Users className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Afiliasi & Referral</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Kelola program afiliasi dan referral.</p>
        </Card>
        <Card className="p-6 hover:bg-gray-50 dark:hover:bg-gray-700/50 cursor-pointer transition-colors duration-200" onClick={() => navigate("/cms/monetization/revenue-analytics")}>
          <div className="flex items-center mb-4">
            <BarChart className="h-8 w-8 text-brand-green mr-3" />
            <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Analitik Pendapatan</h3>
          </div>
          <p className="text-gray-600 dark:text-gray-300">Lihat insight dan tren pendapatan.</p>
        </Card>
      </div>
    </div>
  );
};

export default MonetizationIndex;

/== cms/monetization/revenue-analytics.tsx
import React from 'react';
import RevenueAnalytics from '../../../components/organisms/RevenueAnalytics';

const RevenueAnalyticsPage = () => {
  return (
    <div className="revenue-analytics-container">
      <header className="analytics-header">
        <h1>Analitik Pendapatan</h1>
        <div className="header-actions">
          <button className="settings-button">Pengaturan Analitik</button>
          <button className="export-button">Ekspor Data</button>
        </div>
      </header>
      <main>
        <RevenueAnalytics />
      </main>
    </div>
  );
};

export default RevenueAnalyticsPage;

/== cms/monetization/subscription/EditSubscriptionPage.tsx
import React, { useState, useEffect } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import Button from '../../../../components/atoms/Button';
import Heading from '../../../../components/atoms/Heading';
import Input from '../../../../components/atoms/Input';
import Text from '../../../../components/atoms/Text';
import Toast, { ToastType } from '../../../../components/atoms/Toast';
import { useSubscriptionStore, Subscription } from '../../../../store/subscriptionStore'; // Adjust the import path

const EditSubscriptionPage: React.FC = () => {
  const navigate = useNavigate();
  const { id } = useParams<{ id: string }>();
  const { subscriptions, updateSubscription } = useSubscriptionStore();
  
  const [toast, setToast] = useState<{ show: boolean; message: string; type: ToastType }>({ 
    show: false, message: '', type: 'info' 
  });
  
  const [editForm, setEditForm] = useState<Subscription>({
    id: '',
    name: '',
    price: 0,
    duration: 1,
    features: []
  });

  // Load subscription data when component mounts
  useEffect(() => {
    if (id) {
      const subscription = subscriptions.find(sub => sub.id === id);
      if (subscription) {
        setEditForm({
          ...subscription,
          features: [...subscription.features]
        });
      } else {
        // Subscription not found, show error and navigate back
        setToast({ 
          show: true, 
          message: 'Paket langganan tidak ditemukan', 
          type: 'error' 
        });
        setTimeout(() => {
          navigate('/cms/monetization/subscription');
        }, 2000);
      }
    }
  }, [id, subscriptions, navigate]);

  const handleSave = () => {
    updateSubscription(editForm);
    setToast({ 
      show: true, 
      message: 'Paket langganan berhasil diperbarui', 
      type: 'success' 
    });
    setTimeout(() => {
      navigate('/subscriptions');
    }, 1500);
  };

  const handleCancel = () => {
    navigate('/subscriptions');
  };

  const handleAddFeature = () => {
    setEditForm(prev => ({
      ...prev,
      features: [...prev.features, { name: '', included: true }]
    }));
  };

  const handleFeatureChange = (index: number, field: 'name' | 'included', value: string | boolean) => {
    const newFeatures = [...editForm.features];
    newFeatures[index] = { ...newFeatures[index], [field]: value };
    setEditForm(prev => ({ ...prev, features: newFeatures }));
  };
  
  return (
    <div className="p-6 max-w-3xl mx-auto">
      <div className="flex justify-between items-center mb-6">
        <Heading level={2}>Edit Paket Langganan</Heading>
      </div>

      <div className="bg-white rounded-lg shadow-md p-6">
        <div className="space-y-4">
          <Input
            label="Nama Paket"
            value={editForm.name}
            onChange={e => setEditForm(prev => ({ ...prev, name: e.target.value }))}
          />
          
          <Input
            label="Harga"
            type="number"
            value={editForm.price}
            onChange={e => setEditForm(prev => ({ ...prev, price: Number(e.target.value) }))}
          />
          
          <Input
            label="Durasi (bulan)"
            type="number"
            value={editForm.duration}
            onChange={e => setEditForm(prev => ({ ...prev, duration: Number(e.target.value) }))}
          />
          
          <div className="form-control">
            <label className="flex items-center space-x-2">
              <input
                type="checkbox"
                checked={editForm.isPopular || false}
                onChange={e => setEditForm(prev => ({ ...prev, isPopular: e.target.checked }))}
                className="checkbox"
              />
              <span>Paket Populer</span>
            </label>
          </div>
          
          <div>
            <Text className="font-medium mb-2">Fitur-fitur</Text>
            {editForm.features.map((feature, index) => (
              <div key={index} className="flex items-center space-x-2 mb-2">
                <Input
                  value={feature.name}
                  onChange={e => handleFeatureChange(index, 'name', e.target.value)}
                  placeholder="Nama fitur"
                />
                <label className="flex items-center">
                  <input
                    type="checkbox"
                    checked={feature.included}
                    onChange={e => handleFeatureChange(index, 'included', e.target.checked)}
                    className="mr-2"
                  />
                  <span>Termasuk</span>
                </label>
              </div>
            ))}
            <Button
              onClick={handleAddFeature}
              className="mt-2 bg-gray-500 hover:bg-gray-600"
            >
              Tambah Fitur
            </Button>
          </div>

          <div className="flex justify-end space-x-2 mt-6">
            <Button
              onClick={handleCancel}
              className="bg-gray-500 hover:bg-gray-600"
            >
              Batal
            </Button>
            <Button
              onClick={handleSave}
              className="bg-blue-500 hover:bg-blue-600"
            >
              Simpan
            </Button>
          </div>
        </div>
      </div>

      {toast.show && (
        <Toast
          message={toast.message}
          type={toast.type}
          onClose={() => setToast({ ...toast, show: false })}
        />
      )}
    </div>
  );
};

export default EditSubscriptionPage;

/== cms/monetization/subscription/SubscriptionManagement.tsx
import React, { useState, useEffect } from 'react';
import Button from '../../../../components/atoms/Button';
import Heading from '../../../../components/atoms/Heading';
import Toast, { ToastType } from '../../../../components/atoms/Toast';
import SubscriptionList from '../../../../components/organisms/SubscriptionList';
import { v4 as uuidv4 } from 'uuid';
import { useSubscriptionStore } from '../../../../store/subscriptionStore';

const SubscriptionManagement: React.FC = () => {
  const { subscriptions, addSubscription, initializeStore } = useSubscriptionStore();
  const [toast, setToast] = useState<{ show: boolean; message: string; type: ToastType }>({ 
    show: false, message: '', type: 'info' 
  });
  
  // Initialize store with sample data if empty
  useEffect(() => {
    if (subscriptions.length === 0) {
      initializeStore();
    }
  }, [subscriptions.length, initializeStore]);

  const [newSubscription, setNewSubscription] = useState({
    name: '',
    price: 0,
    duration: 1,
    features: [] as { name: string; included: boolean }[]
  });

  const showToast = (message: string, type: ToastType) => {
    setToast({ show: true, message, type });
  };

  const handleAddSubscription = () => {
    // Create a new subscription object with proper structure for the store
    const subscription = {
      id: uuidv4(),
      type: 'custom', // Or any default type
      title: 'New Subscription', // Default title
      subtitle: 'Custom plan', // Default subtitle
      monthlyPrice: 0, // Default monthly price
      yearlyPrice: 0, // Default yearly price
      buttonText: 'Subscribe', // Default button text
      features: [
        { name: 'Basic feature', included: true }
      ],
      isPopular: false
    };
    
    // Add the subscription to the store
    addSubscription(subscription);
    
    // Reset the form state
    setNewSubscription({ 
      name: '', 
      price: 0, 
      duration: 1, 
      features: [] 
    });
    
    // Show success message
    showToast('Paket langganan berhasil ditambahkan', 'success');
  };

  return (
    <div className="p-6">
      <div className="flex justify-between items-center mb-6">
        <Heading level={2}>Manajemen Langganan</Heading>
        {/* Fixed the onClick handler */}
        <Button onClick={handleAddSubscription}>Tambah Paket Langganan</Button>
      </div>

      <SubscriptionList />

      {toast.show && (
        <Toast
          message={toast.message}
          type={toast.type}
          onClose={() => setToast({ ...toast, show: false })}
        />
      )}
    </div>
  );
};

export default SubscriptionManagement;

/== cms/user-management/AdminUserActivity.tsx
import React, { useState } from 'react';
import { Search, Filter, Activity, Clock, User, BookOpen, Award } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminUserActivity = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [selectedFilter, setSelectedFilter] = useState('all');

  const mockActivities = [
    {
      id: '1',
      user: 'Sarah Chen',
      action: 'completed_course',
      details: 'Completed Python Basics course',
      timestamp: '2024-03-20 14:30',
      type: 'achievement'
    },
    {
      id: '2',
      user: 'Michael Rodriguez',
      action: 'started_course',
      details: 'Started JavaScript Fundamentals course',
      timestamp: '2024-03-20 13:15',
      type: 'learning'
    },
    {
      id: '3',
      user: 'Emma Wilson',
      action: 'earned_certificate',
      details: 'Earned React Advanced Certificate',
      timestamp: '2024-03-20 12:00',
      type: 'achievement'
    }
  ];

  const getActivityIcon = (type: string) => {
    switch (type) {
      case 'achievement':
        return <Award className="h-5 w-5 text-green-500" />;
      case 'learning':
        return <BookOpen className="h-5 w-5 text-blue-500" />;
      default:
        return <Activity className="h-5 w-5 text-gray-500" />;
    }
  };

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">User Activity</h1>
        <div className="flex space-x-4">
          <select
            value={selectedFilter}
            onChange={(e) => setSelectedFilter(e.target.value)}
            className="border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white px-4 py-2"
          >
            <option value="all">All Activities</option>
            <option value="achievement">Achievements</option>
            <option value="learning">Learning</option>
          </select>
          <Button
            variant="outline"
            className="flex items-center"
          >
            <Filter className="h-5 w-5 mr-2" />
            Filter
          </Button>
        </div>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search activities..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {mockActivities.map((activity) => (
            <div key={activity.id} className="p-4 hover:bg-gray-50 dark:hover:bg-gray-700/50">
              <div className="flex items-start">
                <div className="flex-shrink-0">
                  {getActivityIcon(activity.type)}
                </div>
                <div className="ml-4 flex-1">
                  <div className="flex items-center justify-between">
                    <div className="flex items-center">
                      <User className="h-5 w-5 text-gray-400 mr-2" />
                      <span className="font-medium text-gray-900 dark:text-white">
                        {activity.user}
                      </span>
                    </div>
                    <div className="flex items-center text-sm text-gray-500 dark:text-gray-400">
                      <Clock className="h-4 w-4 mr-1" />
                      {activity.timestamp}
                    </div>
                  </div>
                  <p className="mt-1 text-gray-600 dark:text-gray-300">
                    {activity.details}
                  </p>
                </div>
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
};

export default AdminUserActivity;

/== cms/user-management/AdminUserProgress.tsx
import React, { useState } from 'react';
import { Search, Filter, TrendingUp, Award, Clock, BookOpen, Star } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';
import Progress from '../../../components/atoms/Progress';

const AdminUserProgress = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [selectedFilter, setSelectedFilter] = useState('all');

  const mockProgress = [
    {
      id: '1',
      user: 'Sarah Chen',
      course: 'Python Basics',
      progress: 85,
      completedLessons: 17,
      totalLessons: 20,
      lastActivity: '2024-03-20',
      grade: 'A'
    },
    {
      id: '2',
      user: 'Michael Rodriguez',
      course: 'JavaScript Fundamentals',
      progress: 60,
      completedLessons: 12,
      totalLessons: 20,
      lastActivity: '2024-03-19',
      grade: 'B'
    },
    {
      id: '3',
      user: 'Emma Wilson',
      course: 'React Advanced',
      progress: 100,
      completedLessons: 15,
      totalLessons: 15,
      lastActivity: '2024-03-18',
      grade: 'A+'
    }
  ];

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">User Progress</h1>
        <div className="flex space-x-4">
          <select
            value={selectedFilter}
            onChange={(e) => setSelectedFilter(e.target.value)}
            className="border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white px-4 py-2"
          >
            <option value="all">All Courses</option>
            <option value="in_progress">In Progress</option>
            <option value="completed">Completed</option>
          </select>
          <Button
            variant="outline"
            className="flex items-center"
          >
            <Filter className="h-5 w-5 mr-2" />
            Filter
          </Button>
        </div>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search users or courses..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="divide-y divide-gray-200 dark:divide-gray-700">
          {mockProgress.map((item) => (
            <div key={item.id} className="p-6">
              <div className="flex items-start justify-between mb-4">
                <div>
                  <div className="flex items-center">
                    <h3 className="font-medium text-gray-900 dark:text-white">{item.user}</h3>
                    <span className="mx-2 text-gray-300 dark:text-gray-600">•</span>
                    <span className="text-gray-500 dark:text-gray-400">{item.course}</span>
                  </div>
                  <div className="flex items-center mt-1 text-sm text-gray-500 dark:text-gray-400">
                    <Clock className="h-4 w-4 mr-1" />
                    Last active: {item.lastActivity}
                  </div>
                </div>
                <div className="flex items-center">
                  <div className="text-right mr-4">
                    <div className="text-sm font-medium text-gray-900 dark:text-white">
                      Grade
                    </div>
                    <div className="text-2xl font-bold text-brand-green">
                      {item.grade}
                    </div>
                  </div>
                  {item.progress === 100 && (
                    <Award className="h-6 w-6 text-yellow-400" />
                  )}
                </div>
              </div>

              <div className="space-y-4">
                <div>
                  <div className="flex justify-between mb-1">
                    <div className="flex items-center text-sm text-gray-500 dark:text-gray-400">
                      <TrendingUp className="h-4 w-4 mr-1" />
                      Overall Progress
                    </div>
                    <span className="text-sm font-medium text-gray-900 dark:text-white">
                      {item.progress}%
                    </span>
                  </div>
                  <Progress value={item.progress} color="primary" />
                </div>

                <div className="grid grid-cols-3 gap-4">
                  <div className="bg-gray-50 dark:bg-gray-700/50 rounded-lg p-3">
                    <div className="flex items-center text-sm text-gray-500 dark:text-gray-400 mb-1">
                      <BookOpen className="h-4 w-4 mr-1" />
                      Lessons
                    </div>
                    <div className="font-medium text-gray-900 dark:text-white">
                      {item.completedLessons}/{item.totalLessons}
                    </div>
                  </div>

                  <div className="bg-gray-50 dark:bg-gray-700/50 rounded-lg p-3">
                    <div className="flex items-center text-sm text-gray-500 dark:text-gray-400 mb-1">
                      <Star className="h-4 w-4 mr-1" />
                      Achievements
                    </div>
                    <div className="font-medium text-gray-900 dark:text-white">
                      {Math.floor(item.progress / 20)}
                    </div>
                  </div>

                  <div className="bg-gray-50 dark:bg-gray-700/50 rounded-lg p-3">
                    <div className="flex items-center text-sm text-gray-500 dark:text-gray-400 mb-1">
                      <Clock className="h-4 w-4 mr-1" />
                      Time Spent
                    </div>
                    <div className="font-medium text-gray-900 dark:text-white">
                      {Math.floor(item.completedLessons * 1.5)}h
                    </div>
                  </div>
                </div>
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
};

export default AdminUserProgress;

/== cms/user-management/AdminUserRoles.tsx
import React, { useState } from 'react';
import { Plus, Search, Edit, Trash, Shield, Users } from 'lucide-react';
import Card from '../../../components/atoms/Card';
import Button from '../../../components/atoms/Button';

const AdminUserRoles = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [roles, setRoles] = useState([
    {
      id: '1',
      name: 'Admin',
      description: 'Full system access',
      permissions: ['manage_users', 'manage_courses', 'manage_content', 'manage_settings'],
      usersCount: 2
    },
    {
      id: '2',
      name: 'Instructor',
      description: 'Course management and content creation',
      permissions: ['manage_courses', 'manage_content'],
      usersCount: 8
    },
    {
      id: '3',
      name: 'Student',
      description: 'Course access and learning',
      permissions: ['access_courses', 'submit_assignments'],
      usersCount: 245
    }
  ]);

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold text-gray-900 dark:text-white">User Roles</h1>
        <Button
          variant="primary"
          className="bg-brand-green hover:bg-brand-green/90"
        >
          <Plus className="h-5 w-5 mr-2" />
          Add Role
        </Button>
      </div>

      <Card className="overflow-hidden">
        <div className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="relative">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400" />
            <input
              type="text"
              placeholder="Search roles..."
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
              className="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
            />
          </div>
        </div>

        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50 dark:bg-gray-700">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Role
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Permissions
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Users
                </th>
                <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
              {roles.map((role) => (
                <tr key={role.id}>
                  <td className="px-6 py-4">
                    <div className="flex items-center">
                      <Shield className="h-5 w-5 text-gray-400 mr-3" />
                      <div>
                        <div className="font-medium text-gray-900 dark:text-white">
                          {role.name}
                        </div>
                        <div className="text-sm text-gray-500 dark:text-gray-400">
                          {role.description}
                        </div>
                      </div>
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex flex-wrap gap-2">
                      {role.permissions.map((permission, index) => (
                        <span
                          key={index}
                          className="px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded-full"
                        >
                          {permission}
                        </span>
                      ))}
                    </div>
                  </td>
                  <td className="px-6 py-4">
                    <div className="flex items-center text-gray-500 dark:text-gray-400">
                      <Users className="h-5 w-5 mr-2" />
                      {role.usersCount} users
                    </div>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <Button
                      variant="outline"
                      className="mr-2"
                    >
                      <Edit className="h-5 w-5" />
                    </Button>
                    <Button
                      variant="outline"
                      className="text-red-500 hover:text-red-600"
                      disabled={role.usersCount > 0}
                    >
                      <Trash className="h-5 w-5" />
                    </Button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
};

export default AdminUserRoles;

/== my-learning.tsx
import React, { useEffect, useState } from 'react';
import { Link } from 'react-router-dom';
import Badge from '../components/atoms/Badge';
import Button from '../components/atoms/Button';
import useBundleStore from '../store/useBundleStore';

interface UserProgress {
  bundleId: string;
  progress: number;
  lastAccessed: Date;
  coursesCompleted: number;
  totalCourses: number;
}

const MyLearningPage: React.FC = () => {
  const { bundles, courses, fetchBundles, fetchCourses, isLoading } = useBundleStore();
  const [userBundles, setUserBundles] = useState<string[]>([]);
  const [progress, setProgress] = useState<UserProgress[]>([]);
  const [activeTab, setActiveTab] = useState<'bundles' | 'recommended'>('bundles');

  useEffect(() => {
    fetchBundles();
    fetchCourses();
  }, [fetchBundles, fetchCourses]);

  useEffect(() => {
    // Simulate fetching user's purchased bundles
    // In a real application, this would come from your API
    setTimeout(() => {
      if (bundles.length > 0) {
        // For demo purposes, let's assume the user has purchased the first two bundles
        const purchasedBundleIds = bundles.slice(0, 2).map(bundle => bundle.id);
        setUserBundles(purchasedBundleIds);
        
        // Generate random progress for each bundle
        const userProgress: UserProgress[] = purchasedBundleIds.map(bundleId => {
          const bundle = bundles.find(b => b.id === bundleId);
          const totalCourses = bundle?.courses.length || 0;
          const coursesCompleted = Math.floor(Math.random() * (totalCourses + 1));
          const progress = totalCourses > 0 ? (coursesCompleted / totalCourses) * 100 : 0;
          
          return {
            bundleId,
            progress,
            lastAccessed: new Date(Date.now() - Math.random() * 7 * 24 * 60 * 60 * 1000), // Random date in the last week
            coursesCompleted,
            totalCourses,
          };
        });
        
        setProgress(userProgress);
      }
    }, 1000);
  }, [bundles]);

  const getUserBundles = () => {
    return bundles.filter(bundle => userBundles.includes(bundle.id));
  };

  const getBundleProgress = (bundleId: string) => {
    return progress.find(p => p.bundleId === bundleId);
  };

  const formatLastAccessedDate = (date: Date) => {
    const now = new Date();
    const diffMs = now.getTime() - date.getTime();
    const diffDays = Math.floor(diffMs / (1000 * 60 * 60 * 24));
    
    if (diffDays === 0) return 'Today';
    if (diffDays === 1) return 'Yesterday';
    if (diffDays < 7) return `${diffDays} days ago`;
    return date.toLocaleDateString();
  };

  if (isLoading) {
    return (
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="animate-pulse space-y-8">
          <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-1/4"></div>
          <div className="h-6 bg-gray-200 dark:bg-gray-700 rounded w-1/2"></div>
          
          {/* Tab skeleton */}
          <div className="flex space-x-2 border-b border-gray-200 dark:border-gray-700">
            <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-32"></div>
            <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-32 opacity-60"></div>
          </div>
          
          {/* Bundle card skeletons */}
          {[1, 2].map(i => (
            <div key={i} className="bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden">
              <div className="md:flex">
                <div className="md:w-2/3 p-6 space-y-4">
                  <div className="h-6 bg-gray-200 dark:bg-gray-700 rounded w-3/4"></div>
                  <div className="h-4 bg-gray-200 dark:bg-gray-700 rounded w-full"></div>
                  <div className="h-8 bg-gray-200 dark:bg-gray-700 rounded w-1/4"></div>
                  <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded w-full"></div>
                  <div className="h-10 bg-gray-200 dark:bg-gray-700 rounded w-1/3"></div>
                </div>
                <div className="md:w-1/3 bg-gray-50 dark:bg-gray-900 p-6 space-y-4">
                  <div className="h-5 bg-gray-200 dark:bg-gray-700 rounded w-1/2"></div>
                  {[1, 2, 3].map(j => (
                    <div key={j} className="h-16 bg-gray-200 dark:bg-gray-700 rounded"></div>
                  ))}
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    );
  }

  const userBundlesList = getUserBundles();
  const recommendedBundles = bundles
    .filter(bundle => !userBundles.includes(bundle.id) && bundle.isActive)
    .slice(0, 6);

  return (
    <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      {/* Header */}
      <div className="mb-8">
        <h1 className="text-3xl font-bold text-gray-900 dark:text-white mb-2">My Learning</h1>
        <p className="text-lg text-gray-600 dark:text-gray-400">Track your progress through courses and bundles</p>
      </div>
      
      {/* Tabs */}
      <div className="mb-8 border-b border-gray-200 dark:border-gray-700">
        <div className="flex space-x-8">
          <button
            onClick={() => setActiveTab('bundles')}
            className={`py-4 px-1 font-medium text-sm border-b-2 transition-colors duration-200 ease-in-out ${
              activeTab === 'bundles'
                ? 'border-blue-600 text-blue-600 dark:text-blue-400 dark:border-blue-400'
                : 'border-transparent text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-300'
            }`}
          >
            My Bundles
            {userBundlesList.length > 0 && (
              <span className="ml-2 px-2.5 py-0.5 rounded-full text-xs font-medium bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200">
                {userBundlesList.length}
              </span>
            )}
          </button>
          <button
            onClick={() => setActiveTab('recommended')}
            className={`py-4 px-1 font-medium text-sm border-b-2 transition-colors duration-200 ease-in-out ${
              activeTab === 'recommended'
                ? 'border-blue-600 text-blue-600 dark:text-blue-400 dark:border-blue-400'
                : 'border-transparent text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-300'
            }`}
          >
            Recommended for You
            {recommendedBundles.length > 0 && (
              <span className="ml-2 px-2.5 py-0.5 rounded-full text-xs font-medium bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300">
                {recommendedBundles.length}
              </span>
            )}
          </button>
        </div>
      </div>
      
      {/* My Bundles Tab */}
      {activeTab === 'bundles' && (
        <div className="space-y-8">
          {userBundlesList.length === 0 ? (
            <div className="bg-white dark:bg-gray-800 p-8 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 text-center">
              <div className="inline-flex items-center justify-center w-16 h-16 rounded-full bg-blue-50 dark:bg-blue-900 mb-4">
                <svg className="w-8 h-8 text-blue-500 dark:text-blue-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253" />
                </svg>
              </div>
              <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-2">No bundles purchased yet</h3>
              <p className="text-gray-500 dark:text-gray-400 mb-6 max-w-md mx-auto">Browse our bundle collection to find the perfect learning path for your educational journey</p>
              <Link to="/bundles">
                <Button>Explore Bundles</Button>
              </Link>
            </div>
          ) : (
            <div className="grid grid-cols-1 gap-8">
              {userBundlesList.map(bundle => {
                const bundleProgress = getBundleProgress(bundle.id);
                return (
                  <div key={bundle.id} className="bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden transition-all duration-200 hover:shadow-md">
                    <div className="md:flex">
                      <div className="md:w-2/3 p-6">
                        <div className="flex justify-between items-start mb-4">
                          <div>
                            <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-2">{bundle.title}</h3>
                            <p className="text-gray-600 dark:text-gray-400 mb-3">{bundle.description}</p>
                            <div className="flex flex-wrap gap-2">
                              <Badge variant="info" className="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200">
                                {bundle.theme}
                              </Badge>
                              <Badge 
                                variant={
                                  bundle.difficultyLevel === 'beginner' ? 'info' : 
                                  bundle.difficultyLevel === 'intermediate' ? 'warning' : 'danger'
                                }
                                className={
                                  bundle.difficultyLevel === 'beginner' 
                                    ? 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-200' 
                                    : bundle.difficultyLevel === 'intermediate'
                                    ? 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-200'
                                    : 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-200'
                                }
                              >
                                {bundle.difficultyLevel}
                              </Badge>
                            </div>
                          </div>
                          <div className="text-right text-sm text-gray-500 dark:text-gray-400 flex items-center">
                            <svg className="w-4 h-4 mr-1 text-gray-400 dark:text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                            {formatLastAccessedDate(bundleProgress?.lastAccessed || new Date())}
                          </div>
                        </div>
                        
                        <div className="mb-6">
                          <div className="flex justify-between text-sm mb-2">
                            <span className="font-medium text-gray-700 dark:text-gray-300">
                              {bundleProgress?.coursesCompleted || 0} of {bundleProgress?.totalCourses || 0} courses completed
                            </span>
                            <span className="font-semibold text-blue-600 dark:text-blue-400">
                              {Math.round(bundleProgress?.progress || 0)}%
                            </span>
                          </div>
                          <div className="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-2.5 overflow-hidden">
                            <div
                              className="h-2.5 rounded-full bg-blue-600 dark:bg-blue-500 transition-all duration-500 ease-in-out"
                              style={{ width: `${bundleProgress?.progress || 0}%` }}
                            ></div>
                          </div>
                        </div>
                        
                        <div>
                          {bundleProgress?.progress === 100 ? (
                            <div className="bg-green-50 dark:bg-green-900/20 border border-green-200 dark:border-green-800 rounded-lg p-4 flex items-center">
                              <svg className="h-6 w-6 text-green-500 dark:text-green-400 mr-3" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                                <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
                              </svg>
                              <div>
                                <span className="text-green-800 dark:text-green-300 font-medium block">Congratulations!</span>
                                <span className="text-green-700 dark:text-green-400 text-sm">You've completed this bundle</span>
                              </div>
                            </div>
                          ) : (
                            <Link to={`/bundles/${bundle.id}`}>
                              <Button className="bg-blue-600 hover:bg-blue-700 text-white dark:bg-blue-500 dark:hover:bg-blue-600 transition-colors duration-200">
                                Continue Learning
                              </Button>
                            </Link>
                          )}
                        </div>
                      </div>
                      
                      <div className="md:w-1/3 bg-gray-50 dark:bg-gray-900/50 p-6 border-t md:border-t-0 md:border-l border-gray-200 dark:border-gray-700">
                        <h4 className="text-sm font-medium text-gray-500 dark:text-gray-400 mb-4 flex items-center">
                          <svg className="w-4 h-4 mr-1" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                          </svg>
                          Course Progress
                        </h4>
                        <div className="space-y-3">
                          {bundle.courses.map((courseId, index) => {
                            const course = courses.find(c => c.id === courseId);
                            // Generate random progress for each course
                            const courseProgress = index < (bundleProgress?.coursesCompleted || 0) 
                              ? 100 
                              : Math.floor(Math.random() * 100);
                            
                            return course ? (
                              <div key={course.id} className="border border-gray-200 dark:border-gray-700 bg-white dark:bg-gray-800 rounded-lg p-3 transition-all duration-200 hover:shadow-sm">
                                <div className="flex justify-between items-start mb-2">
                                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">{course.title}</div>
                                  <Badge 
                                    variant={courseProgress === 100 ? 'success' : 'info'}
                                    className={courseProgress === 100 
                                      ? 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-200' 
                                      : 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200'
                                    }
                                  >
                                    {courseProgress === 100 ? 'Completed' : 'In Progress'}
                                  </Badge>
                                </div>
                                <div className="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-1.5 mb-1 overflow-hidden">
                                  <div
                                    className={`h-1.5 rounded-full transition-all duration-500 ease-in-out ${
                                      courseProgress === 100 
                                        ? 'bg-green-500 dark:bg-green-400' 
                                        : 'bg-blue-500 dark:bg-blue-400'
                                    }`}
                                    style={{ width: `${courseProgress}%` }}
                                  ></div>
                                </div>
                                <div className="flex justify-between items-center">
                                  <Link 
                                    to={`/courses/${course.id}`} 
                                    className="text-xs text-blue-600 dark:text-blue-400 hover:underline"
                                  >
                                    View Course
                                  </Link>
                                  <div className="text-xs text-gray-500 dark:text-gray-400">{courseProgress}%</div>
                                </div>
                              </div>
                            ) : null;
                          })}
                        </div>
                      </div>
                    </div>
                  </div>
                );
              })}
            </div>
          )}
        </div>
      )}
      
      {/* Recommended Bundles Tab */}
      {activeTab === 'recommended' && (
        <div>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            {recommendedBundles.map(bundle => (
              <div 
                key={bundle.id} 
                className="bg-white dark:bg-gray-800 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 overflow-hidden transition-all duration-200 hover:shadow-md flex flex-col"
              >
                <div className="p-6 flex-grow">
                  <div className="flex justify-between items-start mb-4">
                    <div className="flex space-x-2">
                      <Badge 
                        variant={
                          bundle.difficultyLevel === 'beginner' ? 'info' : 
                          bundle.difficultyLevel === 'intermediate' ? 'warning' : 'danger'
                        }
                        className={
                          bundle.difficultyLevel === 'beginner' 
                            ? 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-200' 
                            : bundle.difficultyLevel === 'intermediate'
                            ? 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-200'
                            : 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-200'
                        }
                      >
                        {bundle.difficultyLevel}
                      </Badge>
                      <Badge 
                        variant="info"
                        className="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200"
                      >
                        {bundle.theme}
                      </Badge>
                    </div>
                    {bundle.bundlePrice < bundle.originalPrice && (
                      <Badge 
                        variant="danger"
                        className="bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-200"
                      >
                        {Math.round((1 - bundle.bundlePrice / bundle.originalPrice) * 100)}% OFF
                      </Badge>
                    )}
                  </div>
                  <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-2">{bundle.title}</h3>
                  <p className="text-gray-600 dark:text-gray-400 text-sm mb-4 line-clamp-2">{bundle.description}</p>
                  
                  <div className="flex items-center text-sm text-gray-500 dark:text-gray-400 mb-3 space-x-4">
                    <div className="flex items-center">
                      <svg className="w-4 h-4 mr-1" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253" />
                      </svg>
                      {bundle.courses.length} courses
                    </div>
                    <div className="flex items-center">
                      <svg className="w-4 h-4 mr-1" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                      </svg>
                      {bundle.courses.length * 2} hours
                    </div>
                  </div>
                </div>
                
                <div className="bg-gray-50 dark:bg-gray-900/50 p-4 border-t border-gray-200 dark:border-gray-700">
                  <div className="flex items-center justify-between mb-3">
                    <div className="text-xs text-gray-500 dark:text-gray-400">Bundle price</div>
                    <div className="flex items-baseline space-x-2">
                      <div className="text-xl font-bold text-blue-600 dark:text-blue-400">${bundle.bundlePrice.toFixed(2)}</div>
                      {bundle.bundlePrice < bundle.originalPrice && (
                        <div className="text-sm text-gray-500 dark:text-gray-400 line-through">${bundle.originalPrice.toFixed(2)}</div>
                      )}
                    </div>
                  </div>
                  
                  <div className="flex space-x-2">
                    <Link 
                      to={`/bundles/${bundle.id}`}
                      className="flex-1"
                    >
                      <Button 
                        variant="outline" 
                        className="w-full border border-gray-300 dark:border-gray-600 text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors duration-200"
                      >
                        Preview
                      </Button>
                    </Link>
                    <Button 
                      className="flex-1 bg-blue-600 hover:bg-blue-700 text-white dark:bg-blue-500 dark:hover:bg-blue-600 transition-colors duration-200"
                    >
                      Enroll Now
                    </Button>
                  </div>
                </div>
              </div>
            ))}
          </div>
          
          {recommendedBundles.length === 0 ? (
            <div className="bg-white dark:bg-gray-800 p-8 rounded-xl shadow-sm border border-gray-200 dark:border-gray-700 text-center">
              <div className="inline-flex items-center justify-center w-16 h-16 rounded-full bg-gray-100 dark:bg-gray-700 mb-4">
                <svg className="w-8 h-8 text-gray-500 dark:text-gray-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-2">No recommendations yet</h3>
              <p className="text-gray-500 dark:text-gray-400 mb-6 max-w-md mx-auto">
                We're still learning about your preferences. Complete some courses to get personalized recommendations.
              </p>
              <Link to="/bundles">
                <Button variant="outline">Browse All Bundles</Button>
              </Link>
            </div>
          ) : (
            <div className="mt-8 text-center">
              <Link 
                to="/bundles" 
                className="inline-flex items-center text-blue-600 dark:text-blue-400 hover:text-blue-800 dark:hover:text-blue-300 font-medium"
              >
                View All Bundles
                <svg className="ml-1 w-4 h-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5l7 7-7 7" />
                </svg>
              </Link>
            </div>
          )}
        </div>
      )}
    </div>
  );
};

export default MyLearningPage;

