/== CodeEditor.tsx
import React, { useRef } from 'react';
import Editor from '@monaco-editor/react';

interface CodeEditorProps {
  code: string;
  onChange: (value: string | undefined) => void;
  language?: string;
  theme?: string;
  readOnly?: boolean;
  height?: string;
}

const CodeEditor: React.FC<CodeEditorProps> = ({
  code,
  onChange,
  language = 'python',
  theme = 'vs-dark',
  readOnly = false,
  height = '100%'
}) => {
  const editorRef = useRef<any>(null);

  const handleEditorDidMount = (editor: any) => {
    editorRef.current = editor;
    editor.focus();
  };

  const options = {
    selectOnLineNumbers: true,
    roundedSelection: false,
    readOnly: readOnly,
    cursorStyle: 'line',
    automaticLayout: true,
    minimap: {
      enabled: false
    },
    scrollBeyondLastLine: false,
    fontSize: 14,
    fontFamily: 'Menlo, Monaco, "Courier New", monospace',
    lineNumbers: 'on',
    folding: true,
    lineHeight: 21,
    renderLineHighlight: 'all',
    scrollbar: {
      useShadows: false,
      verticalScrollbarSize: 10,
      horizontalScrollbarSize: 10
    },
    overviewRulerLanes: 0,
    hideCursorInOverviewRuler: true,
    overviewRulerBorder: false
  };

  return (
    <div className="h-full w-full relative">
      <Editor
        height={height}
        language={language}
        theme={theme}
        value={code}
        options={options}
        onChange={onChange}
        onMount={handleEditorDidMount}
        loading={
          <div className="absolute inset-0 flex items-center justify-center bg-gray-900">
            <div className="text-white">Loading editor...</div>
          </div>
        }
      />
    </div>
  );
};

export default CodeEditor;

/== CourseCard.tsx
import React from 'react';
import { Play } from 'lucide-react';
import { Link } from 'react-router-dom';

interface CourseCardProps {
  id: string;
  title: string;
  progress: number;
  color: string;
  totalProblems?: number;
  solvedProblems?: number;
  totalQuizzes?: number;
  solvedQuizzes?: number;
  isRecommended?: boolean;
  questionsCount?: number;
  level?: string;
}

const CourseCard: React.FC<CourseCardProps> = ({
  id,
  title,
  progress,
  color,
  totalProblems,
  solvedProblems,
  totalQuizzes,
  solvedQuizzes,
  isRecommended = false,
  questionsCount,
  level
}) => {
  return (
    <div className={`rounded-lg overflow-hidden shadow-sm border border-gray-200 ${isRecommended ? '' : 'mb-4'}`}>
      <div className={`${color} p-6`}>
        {!isRecommended && (
          <div className="uppercase text-xs font-semibold text-gray-700 mb-1">Course</div>
        )}
        <h3 className="font-bold text-gray-900 text-lg mb-2">{title}</h3>
        
        {!isRecommended && (
          <>
            <div className="flex items-center text-xs text-gray-700 mb-2">
              <span>{progress}% Completed</span>
            </div>
            <div className="w-full bg-gray-200 rounded-full h-1.5">
              <div 
                className="bg-blue-600 h-1.5 rounded-full" 
                style={{ width: `${progress}%` }}
              ></div>
            </div>
          </>
        )}
      </div>
      
      {!isRecommended && totalProblems && totalQuizzes && (
        <div className="grid grid-cols-2 divide-x divide-gray-200">
          <div className="p-4 text-center">
            <div className="text-gray-900 font-medium">{solvedProblems}/{totalProblems}</div>
            <div className="text-xs text-gray-500">Practice Problems</div>
          </div>
          <div className="p-4 text-center">
            <div className="text-gray-900 font-medium">{solvedQuizzes}/{totalQuizzes}</div>
            <div className="text-xs text-gray-500">Quizzes</div>
          </div>
        </div>
      )}
      
      {isRecommended && (
        <div className="p-4">
          <div className="text-xs text-gray-500 mb-1">
            {questionsCount}+ Questions
          </div>
          <div className="text-xs text-gray-500 mb-3">
            {level} Course
          </div>
          <Link
            to={`/courses/${id}`}
            className="block w-full text-center py-2 px-4 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition duration-200 text-sm font-medium"
          >
            View Course
          </Link>
        </div>
      )}
      
      {!isRecommended && (
        <div className="p-4 flex justify-end">
          <Link
            to={`/courses/${id}`}
            className="flex items-center py-2 px-4 bg-purple-600 text-white rounded-md hover:bg-purple-700 transition duration-200 text-sm font-medium"
          >
            <span>Continue Learning</span>
            <Play className="ml-2 h-4 w-4" />
          </Link>
        </div>
      )}
    </div>
  );
};

export default CourseCard;

/== CourseOutlineSidebar.tsx
import React, { useState } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import { ChevronDown, ChevronRight, X, CheckCircle, Circle } from 'lucide-react';
import goCourses from '../data/courses/go';

interface CourseOutlineSidebarProps {
  courseId: string;
  currentLessonId: string;
}

const CourseOutlineSidebar: React.FC<CourseOutlineSidebarProps> = ({ 
  courseId, 
  currentLessonId 
}) => {
  const navigate = useNavigate();
  const [expandedChapters, setExpandedChapters] = useState<string[]>(['basic-syntax']);

  // Get course level from courseId
  const courseLevel = courseId.split('-')[1];
  const course = courseLevel ? goCourses[courseLevel] : null;

  if (!course) {
    return null;
  }

  const toggleChapter = (chapterId: string) => {
    if (expandedChapters.includes(chapterId)) {
      setExpandedChapters(expandedChapters.filter(id => id !== chapterId));
    } else {
      setExpandedChapters([...expandedChapters, chapterId]);
    }
  };

  const handleLessonClick = (lessonId: string) => {
    navigate(`/courses/${courseId}/lessons/${lessonId}`);
  };

  return (
    <div className="w-72 bg-white border-r border-gray-200 overflow-y-auto">
      <div className="p-4 border-b border-gray-200 flex items-center justify-between">
        <div>
          <div className="text-sm text-gray-500">Course Progress</div>
          <h2 className="text-lg font-medium text-gray-900">{course.title}</h2>
        </div>
      </div>
      
      <div className="py-2">
        {Object.entries(course.lessons).map(([lessonId, lesson]) => (
          <div key={lessonId} className="mb-1">
            <button
              onClick={() => toggleChapter(lessonId)}
              className="w-full flex items-center px-4 py-2 text-left hover:bg-gray-50"
            >
              {expandedChapters.includes(lessonId) ? (
                <ChevronDown className="h-4 w-4 text-gray-400 mr-2" />
              ) : (
                <ChevronRight className="h-4 w-4 text-gray-400 mr-2" />
              )}
              <span className="text-sm font-medium">{lesson.title}</span>
            </button>
            
            {expandedChapters.includes(lessonId) && lesson.steps && (
              <div className="ml-6 pl-4 border-l border-gray-200">
                {lesson.steps.map((step) => (
                  <button
                    key={step.id}
                    onClick={() => handleLessonClick(lessonId)}
                    className={`w-full flex items-center py-2 px-3 text-left text-sm ${
                      currentLessonId === lessonId
                        ? 'bg-blue-50 text-blue-700'
                        : 'hover:bg-gray-50 text-gray-700'
                    }`}
                  >
                    <Circle className="h-4 w-4 text-gray-300 mr-2" />
                    {step.title}
                  </button>
                ))}
              </div>
            )}
          </div>
        ))}
      </div>
    </div>
  );
};

export default CourseOutlineSidebar;

/== DailyChallengeCard.tsx
import React from 'react';
import { ArrowRight } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';

const DailyChallengeCard: React.FC = () => {
  const { dailyChallenge } = useCourseStore();
  
  if (!dailyChallenge) return null;
  
  return (
    <div className="bg-white rounded-lg shadow-sm border border-gray-200 p-4 mb-6">
      <div className="flex items-center mb-2">
        <div className="w-5 h-5 bg-green-100 rounded-full flex items-center justify-center mr-2">
          <span className="text-green-600 text-xs">✓</span>
        </div>
        <h3 className="text-sm font-medium text-gray-600">Challenge of the Day</h3>
      </div>
      
      <h2 className="text-lg font-bold text-gray-900 mb-4">{dailyChallenge.title}</h2>
      
      <div className="flex justify-between">
        <button className="flex items-center text-sm font-medium text-blue-600 hover:text-blue-800">
          Start Challenge
          <ArrowRight className="ml-1 h-4 w-4" />
        </button>
        
        <button className="text-sm text-gray-500 hover:text-gray-700">
          View all
        </button>
      </div>
    </div>
  );
};

export default DailyChallengeCard;

/== DailyStreak.tsx
import React from 'react';
import { useCourseStore } from '../store/courseStore';

const DailyStreak: React.FC = () => {
  const { currentStreak, highestStreak } = useCourseStore();
  
  // Days of the week
  const days = ['S', 'M', 'T', 'W', 'T', 'F', 'S'];
  
  // Get current day index (0-6, where 0 is Sunday)
  const currentDayIndex = new Date().getDay();
  
  return (
    <div className="bg-white rounded-lg p-4 mb-6">
      <div className="flex items-center mb-2">
        <span className="text-yellow-500">⚡</span>
        <h3 className="font-medium ml-2">Daily Streak: {currentStreak}</h3>
      </div>
      <p className="text-xs text-gray-500 mb-3">Highest Streak: {highestStreak}</p>
      
      <div className="flex justify-between">
        {days.map((day, index) => (
          <div key={index} className="flex flex-col items-center">
            <span className="text-xs text-gray-500 mb-1">{day}</span>
            <div 
              className={`w-6 h-6 rounded-full flex items-center justify-center text-xs
                ${index === currentDayIndex 
                  ? 'bg-blue-600 text-white' 
                  : index < currentDayIndex 
                    ? 'bg-gray-200' 
                    : 'border border-gray-300'}`}
            >
              {index <= currentDayIndex ? '✓' : ''}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
};

export default DailyStreak;

/== Footer.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Code, Facebook, Twitter, Instagram, Linkedin, Youtube } from 'lucide-react';

const Footer = () => {
  return (
    <footer className="bg-gray-800 text-white">
      <div className="max-w-7xl mx-auto py-12 px-4 sm:px-6 lg:px-8">
        <div className="grid grid-cols-1 md:grid-cols-4 gap-8">
          <div>
            <div className="flex items-center">
              <Code className="h-8 w-8 text-blue-400" />
              <span className="ml-2 text-xl font-bold">Programiz</span>
            </div>
            <p className="mt-4 text-sm text-gray-300">
              Learn to code in Python, C/C++, Java, and other popular programming languages with our easy-to-follow tutorials, examples, online compiler and references.
            </p>
            <div className="mt-6 flex space-x-4">
              <a href="#" className="text-gray-400 hover:text-white">
                <Facebook className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-white">
                <Twitter className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-white">
                <Instagram className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-white">
                <Linkedin className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-white">
                <Youtube className="h-5 w-5" />
              </a>
            </div>
          </div>
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">Tutorials</h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/tutorials/python" className="text-gray-300 hover:text-white">Python</Link>
              </li>
              <li>
                <Link to="/tutorials/javascript" className="text-gray-300 hover:text-white">JavaScript</Link>
              </li>
              <li>
                <Link to="/tutorials/java" className="text-gray-300 hover:text-white">Java</Link>
              </li>
              <li>
                <Link to="/tutorials/c" className="text-gray-300 hover:text-white">C</Link>
              </li>
              <li>
                <Link to="/tutorials/cpp" className="text-gray-300 hover:text-white">C++</Link>
              </li>
              <li>
                <Link to="/tutorials/dsa" className="text-gray-300 hover:text-white">DSA</Link>
              </li>
            </ul>
          </div>
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">Resources</h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/compiler" className="text-gray-300 hover:text-white">Online Compiler</Link>
              </li>
              <li>
                <Link to="/examples" className="text-gray-300 hover:text-white">Examples</Link>
              </li>
              <li>
                <Link to="/courses" className="text-gray-300 hover:text-white">Courses</Link>
              </li>
              <li>
                <Link to="/challenges" className="text-gray-300 hover:text-white">Challenges</Link>
              </li>
              <li>
                <Link to="/blog" className="text-gray-300 hover:text-white">Blog</Link>
              </li>
            </ul>
          </div>
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">Company</h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/about" className="text-gray-300 hover:text-white">About Us</Link>
              </li>
              <li>
                <Link to="/contact" className="text-gray-300 hover:text-white">Contact</Link>
              </li>
              <li>
                <Link to="/privacy" className="text-gray-300 hover:text-white">Privacy Policy</Link>
              </li>
              <li>
                <Link to="/terms" className="text-gray-300 hover:text-white">Terms of Service</Link>
              </li>
            </ul>
          </div>
        </div>
        <div className="mt-12 border-t border-gray-700 pt-8">
          <p className="text-sm text-gray-400">© {new Date().getFullYear()} Programiz Clone. All rights reserved.</p>
        </div>
      </div>
    </footer>
  );
};

export default Footer;

/== LeaderboardCard.tsx
import React, { useState } from 'react';
import { ChevronDown } from 'lucide-react';
import { useCourseStore } from '../store/courseStore';
import { useAuthStore } from '../store/authStore';

const LeaderboardCard: React.FC = () => {
  const [viewMode, setViewMode] = useState<'weekly' | 'monthly' | 'all-time'>('weekly');
  const { leaderboard } = useCourseStore();
  const { user } = useAuthStore();
  
  const handleViewChange = (mode: 'weekly' | 'monthly' | 'all-time') => {
    setViewMode(mode);
  };
  
  return (
    <div className="bg-white rounded-lg p-4">
      <div className="flex justify-between items-center mb-4">
        <h3 className="font-medium">Challenge Leaderboard</h3>
        <div className="relative inline-block text-left">
          <button className="inline-flex items-center text-sm text-gray-700">
            {viewMode === 'weekly' ? 'Weekly' : viewMode === 'monthly' ? 'Monthly' : 'All Time'}
            <ChevronDown className="ml-1 h-4 w-4" />
          </button>
        </div>
      </div>
      
      <div className="space-y-3">
        {leaderboard.slice(0, 10).map((entry, index) => {
          const isCurrentUser = user && entry.name === user.name;
          
          return (
            <div 
              key={entry.id} 
              className={`flex items-center justify-between py-1 px-2 rounded ${isCurrentUser ? 'bg-blue-50' : ''}`}
            >
              <div className="flex items-center">
                <div className="w-6 text-center font-medium text-gray-500">
                  {index < 3 ? (
                    <span className={`
                      ${index === 0 ? 'text-yellow-500' : ''}
                      ${index === 1 ? 'text-gray-400' : ''}
                      ${index === 2 ? 'text-amber-600' : ''}
                    `}>
                      {index + 1}
                    </span>
                  ) : (
                    entry.rank
                  )}
                </div>
                
                <div className="w-8 h-8 rounded-full overflow-hidden ml-2">
                  <img 
                    src={entry.avatar} 
                    alt={entry.name} 
                    className="w-full h-full object-cover"
                  />
                </div>
                
                <span className={`ml-2 text-sm truncate max-w-[120px] ${isCurrentUser ? 'font-medium' : ''}`}>
                  {entry.name}
                </span>
              </div>
              
              <div className="text-sm font-medium">
                {entry.xp} XP
              </div>
            </div>
          );
        })}
      </div>
      
      <div className="mt-4 text-center">
        <button className="text-blue-600 text-sm font-medium">
          View All
        </button>
      </div>
    </div>
  );
};

export default LeaderboardCard;

/== LoginModal.tsx
import React, { useState } from 'react';
import { X } from 'lucide-react';
import { useAuthStore } from '../store/authStore';
import { useNavigate } from 'react-router-dom';
import Button from './atoms/Button';
import Input from './atoms/Input';

interface LoginModalProps {
  isOpen: boolean;
  onClose: () => void;
}

const LoginModal: React.FC<LoginModalProps> = ({ isOpen, onClose }) => {
  const [isLoginView, setIsLoginView] = useState(true);
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [name, setName] = useState('');
  const navigate = useNavigate();

  const { login, register, isLoading, error, clearError } = useAuthStore();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      if (isLoginView) {
        await login(email, password);
      } else {
        await register(name, email, password);
      }
      
      const { error: authError } = useAuthStore.getState();
      if (!authError) {
        onClose();
        navigate('/dashboard');
      }
    } catch (err) {
      console.error('Authentication error:', err);
    }
  };

  const toggleView = () => {
    setIsLoginView(!isLoginView);
    clearError();
  };

  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center z-50">
      <div className="bg-white dark:bg-gray-800 rounded-xl w-full max-w-md p-8 relative">
        <button 
          onClick={onClose}
          className="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-300"
        >
          <X size={20} />
        </button>
        
        <h2 className="text-2xl font-bold text-gray-900 dark:text-white text-center mb-6">
          {isLoginView ? 'Welcome Back!' : 'Create Account'}
        </h2>
        
        {error && (
          <div className="bg-red-100 dark:bg-red-900/50 border border-red-400 dark:border-red-500 text-red-700 dark:text-red-300 px-4 py-3 rounded mb-6">
            {error}
          </div>
        )}
        
        <form onSubmit={handleSubmit} className="space-y-6">
          {!isLoginView && (
            <Input
              id="name"
              type="text"
              label="Full Name"
              value={name}
              onChange={(e) => setName(e.target.value)}
              required
              className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
            />
          )}
          
          <Input
            id="email"
            type="email"
            label="Email Address"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            required
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          <Input
            id="password"
            type="password"
            label="Password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            required
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          <Button
            type="submit"
            variant="primary"
            disabled={isLoading}
            className="w-full bg-brand-green hover:bg-brand-green/90"
          >
            {isLoading ? 'Processing...' : isLoginView ? 'Sign In' : 'Create Account'}
          </Button>
        </form>
        
        <div className="mt-6 text-center">
          <button
            onClick={toggleView}
            className="text-brand-green hover:text-brand-green/90 text-sm font-medium"
          >
            {isLoginView ? "Don't have an account? Sign up" : "Already have an account? Sign in"}
          </button>
        </div>
        
        <div className="mt-8 border-t border-gray-200 dark:border-gray-700 pt-6">
          <p className="text-xs text-gray-500 dark:text-gray-400 text-center">
            For demo purposes, use: unpam@example.com / password123
          </p>
        </div>
      </div>
    </div>
  );
};

export default LoginModal;

/== Navbar.tsx
import React, { useState } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import { Menu, X, ChevronDown, Bell } from 'lucide-react';
import { useAuthStore } from '../store/authStore';
import UserProfile from './UserProfile';
import Button from './atoms/Button';

const Navbar = () => {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [isLanguagesOpen, setIsLanguagesOpen] = useState(false);
  const navigate = useNavigate();
  const { isAuthenticated } = useAuthStore();

  const languages = [
    { name: 'Python', path: '/tutorials/python' },
    { name: 'JavaScript', path: '/tutorials/javascript' },
    { name: 'Java', path: '/tutorials/java' },
    { name: 'C', path: '/tutorials/c' },
    { name: 'C++', path: '/tutorials/cpp' },
    { name: 'DSA', path: '/tutorials/dsa' },
  ];

  return (
    <nav className="bg-white shadow-sm sticky top-0 z-50">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between h-16">
          <div className="flex items-center">
            <Link to="/" className="flex-shrink-0 flex items-center">
              <div className="flex items-center">
                <span className="text-2xl font-bold tracking-tight">CG</span>
                <span className="w-1.5 h-1.5 bg-brand-green rounded-full ml-0.5" />
              </div>
              <div className="ml-2 flex items-center">
                <span className="text-xl font-bold tracking-tight">CODER GROWTH</span>
                <span className="ml-2 text-xs text-gray-500">by Diki Haryadi</span>
              </div>
            </Link>
            <div className="hidden md:ml-6 md:flex md:space-x-8">
              <div className="relative">
                <button
                  onClick={() => setIsLanguagesOpen(!isLanguagesOpen)}
                  className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 hover:text-brand-green"
                >
                  Learn
                  <ChevronDown className="ml-1 h-4 w-4" />
                </button>
                {isLanguagesOpen && (
                  <div className="absolute left-0 mt-2 w-56 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5">
                    <div className="py-1" role="menu" aria-orientation="vertical">
                      {languages.map((language) => (
                        <Link
                          key={language.path}
                          to={language.path}
                          className="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100"
                          role="menuitem"
                          onClick={() => setIsLanguagesOpen(false)}
                        >
                          {language.name}
                        </Link>
                      ))}
                    </div>
                  </div>
                )}
              </div>
              <Link
                to="/courses"
                className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 hover:text-brand-green"
              >
                Courses
              </Link>
              <Link
                to="/compiler"
                className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 hover:text-brand-green"
              >
                Compiler
              </Link>
            </div>
          </div>

          <div className="hidden md:flex items-center space-x-4">
            {isAuthenticated ? (
              <div className="flex items-center space-x-4">
                <button className="relative text-gray-600 hover:text-gray-900">
                  <Bell className="h-6 w-6" />
                  <span className="absolute top-0 right-0 block h-2 w-2 rounded-full bg-red-500"></span>
                </button>
                <Link to="/dashboard">
                  <Button variant="primary" className="bg-brand-green hover:bg-brand-green/90">
                    Dashboard
                  </Button>
                </Link>
                <UserProfile />
              </div>
            ) : (
              <Link to="/login">
                <Button
                  variant="primary"
                  className="bg-brand-green hover:bg-brand-green/90"
                >
                  Sign In
                </Button>
              </Link>
            )}
          </div>

          <div className="-mr-2 flex items-center md:hidden">
            <button
              onClick={() => setIsMenuOpen(!isMenuOpen)}
              className="inline-flex items-center justify-center p-2 rounded-md text-gray-400 hover:text-gray-500 hover:bg-gray-100"
            >
              <span className="sr-only">Open main menu</span>
              {isMenuOpen ? (
                <X className="block h-6 w-6" aria-hidden="true" />
              ) : (
                <Menu className="block h-6 w-6" aria-hidden="true" />
              )}
            </button>
          </div>
        </div>
      </div>

      {/* Mobile menu */}
      {isMenuOpen && (
        <div className="md:hidden">
          <div className="pt-2 pb-3 space-y-1">
            <Link
              to="/courses"
              className="block pl-3 pr-4 py-2 border-l-4 border-transparent text-base font-medium text-gray-600 hover:bg-gray-50 hover:border-gray-300 hover:text-gray-800"
            >
              Courses
            </Link>
            <Link
              to="/compiler"
              className="block pl-3 pr-4 py-2 border-l-4 border-transparent text-base font-medium text-gray-600 hover:bg-gray-50 hover:border-gray-300 hover:text-gray-800"
            >
              Compiler
            </Link>
          </div>
          <div className="pt-4 pb-3 border-t border-gray-200">
            {!isAuthenticated && (
              <div className="mt-3 px-2 space-y-1">
                <Link
                  to="/login"
                  className="block w-full text-left px-3 py-2 rounded-md text-base font-medium text-gray-700 hover:text-gray-900 hover:bg-gray-50"
                >
                  Sign In
                </Link>
              </div>
            )}
          </div>
        </div>
      )}
    </nav>
  );
};

export default Navbar;

/== StatsCard.tsx
import React from 'react';
import { useCourseStore } from '../store/courseStore';

const StatsCard: React.FC = () => {
  const { totalXp, solvedChallenges } = useCourseStore();
  
  return (
    <div className="bg-white rounded-lg shadow-sm border border-gray-200 p-4 mb-6">
      <h3 className="font-medium text-gray-900 mb-4">Your Stats</h3>
      
      <div className="space-y-4">
        <div className="flex items-center">
          <div className="w-8 h-8 bg-green-100 rounded-full flex items-center justify-center mr-3">
            <span className="text-green-600 text-lg">✓</span>
          </div>
          <div>
            <div className="font-medium text-gray-900">{totalXp}</div>
            <div className="text-xs text-gray-500">Total XP gained</div>
          </div>
        </div>
        
        <div className="flex items-center">
          <div className="w-8 h-8 bg-blue-100 rounded-full flex items-center justify-center mr-3">
            <span className="text-blue-600 text-lg">🏆</span>
          </div>
          <div>
            <div className="font-medium text-gray-900">{solvedChallenges}</div>
            <div className="text-xs text-gray-500">Challenges solved</div>
          </div>
        </div>
        
        <div className="flex items-center">
          <div className="w-8 h-8 bg-purple-100 rounded-full flex items-center justify-center mr-3">
            <span className="text-purple-600 text-lg">📚</span>
          </div>
          <div>
            <div className="font-medium text-gray-900">1</div>
            <div className="text-xs text-gray-500">Courses in progress</div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default StatsCard;

/== UserProfile.tsx
import React, { useState } from 'react';
import { useAuthStore } from '../store/authStore';
import { LogOut, ChevronDown } from 'lucide-react';

const UserProfile: React.FC = () => {
  const { user, logout } = useAuthStore();
  const [isDropdownOpen, setIsDropdownOpen] = useState(false);

  const toggleDropdown = () => {
    setIsDropdownOpen(!isDropdownOpen);
  };

  if (!user) return null;

  return (
    <div className="relative">
      <button
        onClick={toggleDropdown}
        className="flex items-center space-x-2 focus:outline-none"
      >
        <div className="w-8 h-8 rounded-full overflow-hidden bg-blue-600 flex items-center justify-center text-white">
          {user.profilePicture ? (
            <img src={user.profilePicture} alt={user.name} className="w-full h-full object-cover" />
          ) : (
            <span>{user.name.charAt(0)}</span>
          )}
        </div>
        <ChevronDown className="h-4 w-4 text-gray-600" />
      </button>

      {isDropdownOpen && (
        <div className="absolute right-0 mt-2 w-64 bg-white rounded-lg shadow-lg py-2 z-50 border border-gray-200">
          <div className="px-4 py-3 border-b border-gray-100">
            <p className="text-sm font-medium text-gray-900">Hey {user.name},</p>
            <p className="text-xs text-gray-500 mt-1">{user.email}</p>
          </div>
          
          <a href="/profile" className="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">
            My Profile
          </a>
          <a href="/settings" className="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">
            Settings
          </a>
          <a href="/my-learning" className="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">
            My Courses
          </a>
          
          <div className="border-t border-gray-100 mt-2 pt-2">
            <button
              onClick={() => {
                logout();
                setIsDropdownOpen(false);
              }}
              className="flex items-center w-full px-4 py-2 text-sm text-red-600 hover:bg-gray-100"
            >
              <LogOut className="h-4 w-4 mr-2" />
              Sign Out
            </button>
          </div>
        </div>
      )}
    </div>
  );
};

export default UserProfile;

/== atoms/Alert.tsx
import React from 'react';
import { AlertCircle, CheckCircle, Info, XCircle } from 'lucide-react';

interface AlertProps {
  children: React.ReactNode;
  variant?: 'info' | 'success' | 'warning' | 'error';
  className?: string;
}

const Alert: React.FC<AlertProps> = ({
  children,
  variant = 'info',
  className = ''
}) => {
  const variantStyles = {
    info: 'bg-blue-50 text-blue-800 border-blue-200',
    success: 'bg-green-50 text-green-800 border-green-200',
    warning: 'bg-yellow-50 text-yellow-800 border-yellow-200',
    error: 'bg-red-50 text-red-800 border-red-200'
  };

  const icons = {
    info: Info,
    success: CheckCircle,
    warning: AlertCircle,
    error: XCircle
  };

  const Icon = icons[variant];

  return (
    <div className={`flex items-center p-4 rounded-lg border ${variantStyles[variant]} ${className}`}>
      <Icon className="h-5 w-5 mr-3" />
      <div>{children}</div>
    </div>
  );
};

export default Alert;

/== atoms/AuthInput.tsx
import React from 'react';

interface AuthInputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
}

const AuthInput: React.FC<AuthInputProps> = ({
  label,
  error,
  className = '',
  ...props
}) => {
  return (
    <div className="space-y-1">
      <label className="block text-sm font-medium text-gray-700 dark:text-gray-300">
        {label}
      </label>
      <input
        className={`
          w-full px-4 py-2 rounded-lg border bg-white dark:bg-gray-800
          focus:ring-2 focus:ring-brand-green focus:border-transparent
          ${error ? 'border-red-500' : 'border-gray-300 dark:border-gray-600'}
          ${className}
        `}
        {...props}
      />
      {error && (
        <p className="text-sm text-red-600 dark:text-red-400">{error}</p>
      )}
    </div>
  );
};

export default AuthInput;

/== atoms/Avatar.tsx
import React from 'react';

interface AvatarProps {
  src?: string;
  alt: string;
  size?: 'sm' | 'md' | 'lg';
  fallback?: string;
}

const Avatar: React.FC<AvatarProps> = ({
  src,
  alt,
  size = 'md',
  fallback
}) => {
  const sizeStyles = {
    sm: 'w-8 h-8',
    md: 'w-10 h-10',
    lg: 'w-12 h-12'
  };

  if (!src && !fallback) {
    return (
      <div className={`${sizeStyles[size]} bg-blue-600 rounded-full flex items-center justify-center text-white`}>
        {alt.charAt(0).toUpperCase()}
      </div>
    );
  }

  return (
    <div className={`${sizeStyles[size]} rounded-full overflow-hidden bg-gray-200`}>
      <img
        src={src}
        alt={alt}
        className="w-full h-full object-cover"
        onError={(e) => {
          if (fallback) {
            (e.target as HTMLImageElement).src = fallback;
          }
        }}
      />
    </div>
  );
};

export default Avatar;

/== atoms/Badge.tsx
import React from "react";

interface BadgeProps {
  children: React.ReactNode;
  color?: "default" | "blue" | "green" | "red" | "yellow" | "purple";
  className?: string;
}

const Badge: React.FC<BadgeProps> = ({ 
  children, 
  color = "default", 
  className = ""
}) => {
  const colorClasses = {
    default: "bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300",
    blue: "bg-blue-100 text-blue-800 dark:bg-blue-900 dark:bg-opacity-30 dark:text-blue-300",
    green: "bg-green-100 text-green-800 dark:bg-green-900 dark:bg-opacity-30 dark:text-green-300",
    red: "bg-red-100 text-red-800 dark:bg-red-900 dark:bg-opacity-30 dark:text-red-300",
    yellow: "bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:bg-opacity-30 dark:text-yellow-300",
    purple: "bg-purple-100 text-purple-800 dark:bg-purple-900 dark:bg-opacity-30 dark:text-purple-300",
  };

  return (
    <span
      className={`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium ${colorClasses[color]} ${className}`}
    >
      {children}
    </span>
  );
};

export default Badge;

/== atoms/Button.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';
import { Link } from 'react-router-dom';

interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  icon?: LucideIcon;
  disabled?: boolean;
  className?: string;
  type?: 'button' | 'submit' | 'reset';
  href?: string;
}

const Button: React.FC<ButtonProps> = ({
  children,
  onClick,
  variant = 'primary',
  size = 'md',
  icon: Icon,
  disabled = false,
  className = '',
  type = 'button',
  href
}) => {
  const baseStyles = 'inline-flex items-center justify-center font-medium rounded-md transition-all duration-200';
  
  const variantStyles = {
    primary: 'bg-primary-500 text-white hover:bg-primary-600 dark:bg-primary-600 dark:hover:bg-primary-700 focus:ring-2 focus:ring-primary-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900',
    secondary: 'bg-gray-900 text-white hover:bg-gray-800 dark:bg-gray-700 dark:hover:bg-gray-600 focus:ring-2 focus:ring-gray-900 focus:ring-offset-2 dark:focus:ring-offset-gray-900',
    outline: 'border border-gray-300 dark:border-gray-600 text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-700 focus:ring-2 focus:ring-gray-900 dark:focus:ring-gray-400 focus:ring-offset-2 dark:focus:ring-offset-gray-900'
  };
  
  const sizeStyles = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg'
  };
  
  const disabledStyles = disabled 
    ? 'opacity-50 cursor-not-allowed dark:opacity-40' 
    : 'cursor-pointer';

  const combinedClassName = `${baseStyles} ${variantStyles[variant]} ${sizeStyles[size]} ${disabledStyles} ${className}`;

  const content = (
    <>
      {Icon && <Icon className={`h-5 w-5 ${children ? 'mr-2' : ''}`} />}
      {children}
    </>
  );

  if (href) {
    return (
      <Link to={href} className={combinedClassName}>
        {content}
      </Link>
    );
  }

  return (
    <button
      type={type}
      onClick={onClick}
      disabled={disabled}
      className={combinedClassName}
    >
      {content}
    </button>
  );
};

export default Button;

/== atoms/Card.tsx
import React from 'react';

interface CardProps {
  children: React.ReactNode;
  className?: string;
  onClick?: () => void;
  hoverable?: boolean;
}

const Card: React.FC<CardProps> = ({
  children,
  className = '',
  onClick,
  hoverable = false
}) => {
  return (
    <div
      className={`
       bg-white dark:bg-gray-800 rounded-lg shadow-sm border border-gray-200 dark:border-gray-700  overflow-hidden
        ${hoverable ? 'transition-shadow hover:shadow-lg' : ''}
        ${className}
      `}
      onClick={onClick}
      role={onClick ? 'button' : undefined}
    >
      {children}
    </div>
  );
};

export default Card;

/== atoms/Confetti.tsx
import React, { useEffect, useState } from 'react';

const Confetti: React.FC = () => {
  const [particles, setParticles] = useState<JSX.Element[]>([]);

  useEffect(() => {
    const colors = ['#FFD700', '#FF69B4', '#00CED1', '#98FB98', '#DDA0DD'];
    const particleCount = 100;
    const newParticles: JSX.Element[] = [];

    for (let i = 0; i < particleCount; i++) {
      const left = Math.random() * 100;
      const animationDuration = 3 + Math.random() * 2;
      const color = colors[Math.floor(Math.random() * colors.length)];
      const size = 5 + Math.random() * 10;
      const rotation = Math.random() * 360;

      newParticles.push(
        <div
          key={i}
          className="absolute animate-fall"
          style={{
            left: `${left}%`,
            top: '-20px',
            width: `${size}px`,
            height: `${size}px`,
            backgroundColor: color,
            transform: `rotate(${rotation}deg)`,
            animation: `fall ${animationDuration}s linear infinite`,
          }}
        />
      );
    }

    setParticles(newParticles);

    // Clean up animation after 5 seconds
    const timeout = setTimeout(() => {
      setParticles([]);
    }, 5000);

    return () => clearTimeout(timeout);
  }, []);

  return (
    <div className="fixed inset-0 pointer-events-none overflow-hidden">
      {particles}
      <style>
        {`
          @keyframes fall {
            0% {
              transform: translateY(-20px) rotate(0deg);
              opacity: 1;
            }
            100% {
              transform: translateY(100vh) rotate(360deg);
              opacity: 0;
            }
          }
          .animate-fall {
            animation: fall 3s linear infinite;
          }
        `}
      </style>
    </div>
  );
};

export default Confetti;

/== atoms/Divider.tsx
import React from 'react';

interface DividerProps {
  orientation?: 'horizontal' | 'vertical';
  className?: string;
  children?: React.ReactNode;
}

const Divider: React.FC<DividerProps> = ({
  orientation = 'horizontal',
  className = '',
  children
}) => {
  const baseStyles = {
    horizontal: 'w-full border-t border-gray-200',
    vertical: 'h-full border-l border-gray-200'
  };

  if (children) {
    return (
      <div className="relative">
        <div className="absolute inset-0 flex items-center">
          <div className={`${baseStyles[orientation]} ${className}`} />
        </div>
        <div className="relative flex justify-center">
          <span className="bg-white px-2 text-sm text-gray-500">
            {children}
          </span>
        </div>
      </div>
    );
  }

  return <div className={`${baseStyles[orientation]} ${className}`} />;
};

export default Divider;

/== atoms/Heading.tsx
import React from 'react';

interface HeadingProps {
  children: React.ReactNode;
  level?: number | 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6';
  className?: string;
}

const Heading: React.FC<HeadingProps> = ({
  children,
  level = 'h1',
  className = ''
}) => {
  const baseStyles = 'font-bold text-gray-900';
  const sizeStyles = {
    h1: 'text-4xl',
    h2: 'text-3xl',
    h3: 'text-2xl',
    h4: 'text-xl',
    h5: 'text-lg',
    h6: 'text-base'
  };

  const headingLevel = typeof level === 'number' ? `h${level}` : level;
  const Tag = headingLevel as keyof JSX.IntrinsicElements;

  return (
    <Tag className={`${baseStyles} ${sizeStyles[headingLevel]} ${className}`}>
      {children}
    </Tag>
  );
};

export default Heading;

/== atoms/IconButton.tsx
import React from 'react';
import { LucideIcon } from 'lucide-react';

interface IconButtonProps {
  icon: LucideIcon;
  onClick?: () => void;
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  className?: string;
  title?: string;
  disabled?: boolean;
}

const IconButton: React.FC<IconButtonProps> = ({
  icon: Icon,
  onClick,
  variant = 'primary',
  size = 'md',
  className = '',
  title,
  disabled = false
}) => {
  const baseStyles = 'inline-flex items-center justify-center rounded-full';
  
  const variantStyles = {
    primary: 'bg-blue-600 text-white hover:bg-blue-700',
    secondary: 'bg-gray-200 text-gray-700 hover:bg-gray-300',
    outline: 'border border-gray-300 text-gray-700 hover:bg-gray-50'
  };

  const sizeStyles = {
    sm: 'p-1.5',
    md: 'p-2',
    lg: 'p-3'
  };

  const iconSizes = {
    sm: 'h-4 w-4',
    md: 'h-5 w-5',
    lg: 'h-6 w-6'
  };

  return (
    <button
      onClick={onClick}
      disabled={disabled}
      title={title}
      className={`
        ${baseStyles}
        ${variantStyles[variant]}
        ${sizeStyles[size]}
        ${disabled ? 'opacity-50 cursor-not-allowed' : ''}
        ${className}
      `}
    >
      <Icon className={iconSizes[size]} />
    </button>
  );
};

export default IconButton;

/== atoms/Input.tsx
import React from 'react';

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
}

const Input: React.FC<InputProps> = ({
  label,
  error,
  id,
  className = '',
  ...props
}) => {
  return (
    <div className="w-full">
      {label && (
        <label htmlFor={id} className="block text-sm font-medium text-gray-700 mb-1">
          {label}
        </label>
      )}
      <input
        id={id}
        className={`
          w-full px-3 py-2 border border-gray-300 rounded-md 
          focus:outline-none focus:ring-2 focus:ring-blue-500 
          ${error ? 'border-red-500' : ''} 
          ${className}
        `}
        {...props}
      />
      {error && (
        <p className="mt-1 text-sm text-red-600">{error}</p>
      )}
    </div>
  );
};

export default Input;

/== atoms/Logo.tsx
import React from 'react';
import { Link } from 'react-router-dom';

interface LogoProps {
  showText?: boolean;
}

const Logo: React.FC<LogoProps> = ({ showText = true }) => {
  return (
    <Link to="/" className="flex items-center">
      <div className="flex items-center">
        <span className="text-2xl font-bold tracking-tight">CG</span>
        <span className="w-1.5 h-1.5 bg-green-500 rounded-full ml-0.5" />
      </div>
      {showText && (
        <div className="ml-2 flex items-center">
          <span className="text-xl font-bold tracking-tight">CODER GROWTH</span>
          <span className="ml-2 text-xs text-gray-500">by Diki Haryadi</span>
        </div>
      )}
    </Link>
  );
};

export default Logo;

/== atoms/Modal.tsx
import React, { useEffect, useRef } from 'react';
import { X } from 'lucide-react';
import Button from './Button';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
  footer?: React.ReactNode;
  size?: 'sm' | 'md' | 'lg' | 'xl';
}

const Modal: React.FC<ModalProps> = ({
  isOpen,
  onClose,
  title,
  children,
  footer,
  size = 'md'
}) => {
  const modalRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') {
        onClose();
      }
    };

    const handleClickOutside = (e: MouseEvent) => {
      if (modalRef.current && !modalRef.current.contains(e.target as Node)) {
        onClose();
      }
    };

    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      document.addEventListener('mousedown', handleClickOutside);
      document.body.style.overflow = 'hidden';
    }

    return () => {
      document.removeEventListener('keydown', handleEscape);
      document.removeEventListener('mousedown', handleClickOutside);
      document.body.style.overflow = 'auto';
    };
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  const sizeClasses = {
    sm: 'max-w-md',
    md: 'max-w-lg',
    lg: 'max-w-2xl',
    xl: 'max-w-4xl'
  };

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black bg-opacity-50">
      <div 
        ref={modalRef}
        className={`bg-white dark:bg-gray-800 rounded-lg shadow-xl w-full ${sizeClasses[size]} overflow-hidden`}
      >
        <div className="flex items-center justify-between p-4 border-b border-gray-200 dark:border-gray-700">
          <h3 className="text-lg font-medium text-gray-900 dark:text-white">{title}</h3>
          <button
            onClick={onClose}
            className="text-gray-400 hover:text-gray-500 focus:outline-none"
          >
            <X className="h-5 w-5" />
          </button>
        </div>
        
        <div className="p-6">{children}</div>
        
        {footer && (
          <div className="px-4 py-3 bg-gray-50 dark:bg-gray-700 border-t border-gray-200 dark:border-gray-600 flex justify-end space-x-3">
            {footer}
          </div>
        )}
      </div>
    </div>
  );
};

export default Modal;

/== atoms/PaymentInput.tsx
import React from 'react';

interface PaymentInputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
  icon?: React.ReactNode;
}

const PaymentInput: React.FC<PaymentInputProps> = ({
  label,
  error,
  icon,
  className = '',
  ...props
}) => {
  return (
    <div className="space-y-1">
      <label className="block text-sm font-medium text-gray-700">
        {label}
      </label>
      <div className="relative">
        <input
          className={`
            w-full px-4 py-2 rounded-lg border bg-white
            focus:ring-2 focus:ring-primary-500 focus:border-transparent
            ${error ? 'border-red-500' : 'border-gray-300'}
            ${icon ? 'pl-10' : ''}
            ${className}
          `}
          {...props}
        />
        {icon && (
          <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-gray-400">
            {icon}
          </div>
        )}
      </div>
      {error && (
        <p className="text-sm text-red-600">{error}</p>
      )}
    </div>
  );
};

export default PaymentInput;

/== atoms/PaymentMethodButton.tsx
import React from 'react';

interface PaymentMethodButtonProps {
  icon: React.ReactNode;
  name: string;
  selected: boolean;
  onClick: () => void;
}

const PaymentMethodButton: React.FC<PaymentMethodButtonProps> = ({
  icon,
  name,
  selected,
  onClick
}) => {
  return (
    <button
      onClick={onClick}
      className={`w-full flex items-center justify-center p-4 rounded-lg border ${
        selected 
          ? 'border-primary-500 bg-primary-50 text-primary-600' 
          : 'border-gray-200 hover:bg-gray-50'
      }`}
    >
      {icon}
      <span className="ml-2 font-medium">{name}</span>
    </button>
  );
};

export default PaymentMethodButton;

/== atoms/PricingFeature.tsx
import React from 'react';
import { Check } from 'lucide-react';

interface PricingFeatureProps {
  name: string;
  included: boolean;
}

const PricingFeature: React.FC<PricingFeatureProps> = ({ name, included }) => {
  return (
    <li className="flex items-center">
      <Check 
        className={`h-5 w-5 mr-3 ${
          included ? 'text-green-500' : 'text-gray-300'
        }`} 
      />
      <span className={included ? 'text-gray-700' : 'text-gray-400'}>
        {name}
      </span>
    </li>
  );
};

export default PricingFeature;

/== atoms/PricingToggle.tsx
import React from 'react';

interface PricingToggleProps {
  value: 'yearly' | 'monthly';
  onChange: (value: 'yearly' | 'monthly') => void;
}

const PricingToggle: React.FC<PricingToggleProps> = ({ value, onChange }) => {
  return (
    <div className="bg-white rounded-lg p-1 inline-flex">
      <button
        onClick={() => onChange('yearly')}
        className={`px-4 py-2 rounded-md text-sm font-medium ${
          value === 'yearly'
            ? 'bg-primary-500 text-white'
            : 'text-gray-700 hover:bg-gray-100'
        }`}
      >
        Yearly
      </button>
      <button
        onClick={() => onChange('monthly')}
        className={`px-4 py-2 rounded-md text-sm font-medium ${
          value === 'monthly'
            ? 'bg-primary-500 text-white'
            : 'text-gray-700 hover:bg-gray-100'
        }`}
      >
        Monthly
      </button>
    </div>
  );
};

export default PricingToggle;

/== atoms/Progress.tsx
import React from "react";

interface ProgressProps {
  value: number;
  max?: number;
  color?: "default" | "blue" | "green" | "red" | "yellow";
  className?: string;
}

const Progress: React.FC<ProgressProps> = ({
  value,
  max = 100,
  color = "default",
  className = "",
}) => {
  // Ensure the value is between 0 and max
  const clampedValue = Math.min(Math.max(0, value), max);
  const percentage = (clampedValue / max) * 100;

  const baseClasses = "rounded-full overflow-hidden bg-gray-200 dark:bg-gray-700";
  const barClasses = {
    default: "bg-gray-500 dark:bg-gray-400",
    blue: "bg-blue-500 dark:bg-blue-600",
    green: "bg-green-500 dark:bg-green-600",
    red: "bg-red-500 dark:bg-red-600",
    yellow: "bg-yellow-500 dark:bg-yellow-600",
  };

  return (
    <div className={`${baseClasses} ${className}`}>
      <div
        className={`${barClasses[color]} h-full transition-all duration-300 ease-in-out`}
        style={{ width: `${percentage}%` }}
        role="progressbar"
        aria-valuenow={clampedValue}
        aria-valuemin={0}
        aria-valuemax={max}
      ></div>
    </div>
  );
};

export default Progress;

/== atoms/RoadmapConnection.tsx
import React from 'react';

interface RoadmapConnectionProps {
  type?: 'straight' | 'curved';
  direction?: 'horizontal' | 'vertical';
  className?: string;
}

const RoadmapConnection: React.FC<RoadmapConnectionProps> = ({
  type = 'straight',
  direction = 'horizontal',
  className = ''
}) => {
  if (direction === 'horizontal') {
    return (
      <div className={`h-px bg-gray-300 dark:bg-gray-700 ${className}`} />
    );
  }

  return (
    <div className={`w-px h-full bg-gray-300 dark:bg-gray-700 ${className}`} />
  );
};

export default RoadmapConnection;

/== atoms/RoadmapNode.tsx
import React from 'react';
import { Check } from 'lucide-react';

interface RoadmapNodeProps {
  title: string;
  isCompleted?: boolean;
  isActive?: boolean;
  type?: 'primary' | 'secondary' | 'highlight';
  onClick?: () => void;
}

const RoadmapNode: React.FC<RoadmapNodeProps> = ({
  title,
  isCompleted = false,
  isActive = false,
  type = 'secondary',
  onClick
}) => {
  const baseStyles = "px-4 py-2 rounded-lg font-medium text-sm transition-all duration-200 cursor-pointer";
  
  const typeStyles = {
    primary: "bg-blue-600 text-white hover:bg-blue-700",
    secondary: "bg-yellow-100 text-yellow-900 hover:bg-yellow-200",
    highlight: "bg-purple-600 text-white hover:bg-purple-700"
  };

  const statusStyles = isCompleted 
    ? "border-2 border-green-500" 
    : isActive 
      ? "ring-2 ring-blue-400" 
      : "";

  return (
    <div
      onClick={onClick}
      className={`
        ${baseStyles}
        ${typeStyles[type]}
        ${statusStyles}
        flex items-center justify-between
        hover:scale-105 transform
      `}
    >
      <span>{title}</span>
      {isCompleted && (
        <Check className="h-4 w-4 ml-2 text-green-500" />
      )}
    </div>
  );
};

export default RoadmapNode;

/== atoms/Select.tsx
import React from 'react';

interface SelectOption {
  value: string;
  label: string;
}

interface SelectProps extends React.SelectHTMLAttributes<HTMLSelectElement> {
  options: SelectOption[];
  label?: string;
  error?: string;
  className?: string;
}

const Select: React.FC<SelectProps> = ({
  options,
  label,
  error,
  className = '',
  ...props
}) => {
  return (
    <div className="w-full">
      {label && (
        <label className="block text-sm font-medium text-gray-700 mb-1">
          {label}
        </label>
      )}
      <select
        className={`
          w-full px-3 py-2 bg-white border border-gray-300 rounded-md
          focus:outline-none focus:ring-2 focus:ring-blue-500
          disabled:bg-gray-100 disabled:cursor-not-allowed
          ${error ? 'border-red-500' : 'border-gray-300'}
          ${className}
        `}
        {...props}
      >
        {options.map((option) => (
          <option key={option.value} value={option.value}>
            {option.label}
          </option>
        ))}
      </select>
      {error && (
        <p className="mt-1 text-sm text-red-600">{error}</p>
      )}
    </div>
  );
};

export default Select;

/== atoms/Spinner.tsx
import React from 'react';

interface SpinnerProps {
  size?: 'sm' | 'md' | 'lg';
  color?: 'primary' | 'white';
}

const Spinner: React.FC<SpinnerProps> = ({
  size = 'md',
  color = 'primary'
}) => {
  const sizeStyles = {
    sm: 'w-4 h-4',
    md: 'w-6 h-6',
    lg: 'w-8 h-8'
  };

  const colorStyles = {
    primary: 'text-blue-600',
    white: 'text-white'
  };

  return (
    <div className={`${sizeStyles[size]} ${colorStyles[color]} animate-spin`}>
      <svg className="w-full h-full" viewBox="0 0 24 24">
        <circle
          className="opacity-25"
          cx="12"
          cy="12"
          r="10"
          stroke="currentColor"
          strokeWidth="4"
          fill="none"
        />
        <path
          className="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
        />
      </svg>
    </div>
  );
};

export default Spinner;

/== atoms/Text.tsx
import React from 'react';

interface TextProps {
  children: React.ReactNode;
  variant?: 'body' | 'caption' | 'small';
  color?: 'default' | 'muted' | 'primary' | 'error';
  className?: string;
}

const Text: React.FC<TextProps> = ({
  children,
  variant = 'body',
  color = 'default',
  className = ''
}) => {
  const variantStyles = {
    body: 'text-base',
    caption: 'text-sm',
    small: 'text-xs'
  };

  const colorStyles = {
    default: 'text-gray-900',
    muted: 'text-gray-600',
    primary: 'text-blue-600',
    error: 'text-red-600'
  };

  return (
    <p className={`${variantStyles[variant]} ${colorStyles[color]} ${className}`}>
      {children}
    </p>
  );
};

export default Text;

/== atoms/TextArea.tsx
import React from 'react';

interface TextAreaProps extends React.TextareaHTMLAttributes<HTMLTextAreaElement> {
  label?: string;
  error?: string;
}

const TextArea: React.FC<TextAreaProps> = ({
  label,
  error,
  className = '',
  ...props
}) => {
  return (
    <div className="w-full">
      {label && (
        <label className="block text-sm font-medium text-gray-700 mb-1">
          {label}
        </label>
      )}
      <textarea
        className={`
          w-full px-3 py-2 border border-gray-300 rounded-md
          focus:outline-none focus:ring-2 focus:ring-blue-500
          disabled:bg-gray-100 disabled:cursor-not-allowed
          ${error ? 'border-red-500' : 'border-gray-300'}
          ${className}
        `}
        {...props}
      />
      {error && (
        <p className="mt-1 text-sm text-red-600">{error}</p>
      )}
    </div>
  );
};

export default TextArea;

/== atoms/ThemeToggle.tsx
import React from 'react';
import { Sun, Moon } from 'lucide-react';
import { useThemeStore } from '../../store/themeStore';

const ThemeToggle: React.FC = () => {
  const { isDarkMode, toggleTheme } = useThemeStore();

  React.useEffect(() => {
    if (isDarkMode) {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
  }, [isDarkMode]);

  return (
    <button
      onClick={toggleTheme}
      className="p-2 rounded-md text-gray-500 hover:bg-gray-100 dark:hover:bg-gray-700 dark:text-gray-400"
      aria-label="Toggle theme"
    >
      {isDarkMode ? (
        <Sun className="h-5 w-5" />
      ) : (
        <Moon className="h-5 w-5" />
      )}
    </button>
  );
};

export default ThemeToggle;

/== atoms/Toast.tsx
import React, { useEffect } from 'react';
import { CheckCircle, XCircle, AlertCircle, Info, X } from 'lucide-react';

export type ToastType = 'success' | 'error' | 'warning' | 'info';

interface ToastProps {
  message: string;
  type?: ToastType;
  onClose: () => void;
  duration?: number;
}

const Toast: React.FC<ToastProps> = ({
  message,
  type = 'info',
  onClose,
  duration = 3000
}) => {
  useEffect(() => {
    const timer = setTimeout(() => {
      onClose();
    }, duration);

    return () => clearTimeout(timer);
  }, [duration, onClose]);

  const icons = {
    success: <CheckCircle className="w-5 h-5 text-green-500" />,
    error: <XCircle className="w-5 h-5 text-red-500" />,
    warning: <AlertCircle className="w-5 h-5 text-yellow-500" />,
    info: <Info className="w-5 h-5 text-blue-500" />
  };

  const colors = {
    success: 'bg-green-50 border-green-200',
    error: 'bg-red-50 border-red-200',
    warning: 'bg-yellow-50 border-yellow-200',
    info: 'bg-blue-50 border-blue-200'
  };

  return (
    <div
      className={`fixed top-4 right-4 flex items-center p-4 rounded-lg border ${colors[type]} shadow-lg max-w-sm animate-slide-in-right`}
      role="alert"
    >
      <div className="flex items-center">
        {icons[type]}
        <p className="ml-3 text-sm font-medium text-gray-800">{message}</p>
      </div>
      <button
        onClick={onClose}
        className="ml-4 text-gray-400 hover:text-gray-600"
      >
        <X className="w-4 h-4" />
      </button>
    </div>
  );
};

export default Toast;

/== molecules/AddIntegrationModal.tsx
import React, { useState } from 'react';
import { X } from 'lucide-react';
import Button from '../atoms/Button';
import Input from '../atoms/Input';
import Select from '../atoms/Select';
import Heading from '../atoms/Heading';
import Text from '../atoms/Text';

interface AddIntegrationModalProps {
  isOpen: boolean;
  onClose: () => void;
  onSubmit: (data: any) => void;
  integrationType: 'api' | 'payment' | 'sso' | 'third-party' | 'webhook';
  isLoading?: boolean;
}

const AddIntegrationModal: React.FC<AddIntegrationModalProps> = ({
  isOpen,
  onClose,
  onSubmit,
  integrationType,
  isLoading = false
}) => {
  const [formData, setFormData] = useState<Record<string, any>>({});

  if (!isOpen) return null;

  const handleChange = (field: string, value: any) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit(formData);
  };

  const getFormFields = () => {
    switch (integrationType) {
      case 'api':
        return (
          <>
            <div className="mb-4">
              <label htmlFor="name" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Nama API Key <span className="text-red-500">*</span>
              </label>
              <Input
                id="name"
                type="text"
                placeholder="Contoh: Production API Key"
                value={formData.name || ''}
                onChange={(e) => handleChange('name', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="description" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Deskripsi
              </label>
              <Input
                id="description"
                type="text"
                placeholder="Deskripsi penggunaan API key"
                value={formData.description || ''}
                onChange={(e) => handleChange('description', e.target.value)}
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="expiry" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Tanggal Kadaluarsa
              </label>
              <Input
                id="expiry"
                type="date"
                value={formData.expiry || ''}
                onChange={(e) => handleChange('expiry', e.target.value)}
                className="w-full"
              />
            </div>
          </>
        );
      
      case 'payment':
        return (
          <>
            <div className="mb-4">
              <label htmlFor="provider" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Penyedia Pembayaran <span className="text-red-500">*</span>
              </label>
              <Select
                id="provider"
                value={formData.provider || ''}
                onChange={(e) => handleChange('provider', e.target.value)}
                required
                className="w-full"
              >
                <option value="">Pilih Penyedia</option>
                <option value="stripe">Stripe</option>
                <option value="paypal">PayPal</option>
                <option value="midtrans">Midtrans</option>
                <option value="xendit">Xendit</option>
              </Select>
            </div>
            <div className="mb-4">
              <label htmlFor="apiKey" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                API Key <span className="text-red-500">*</span>
              </label>
              <Input
                id="apiKey"
                type="password"
                placeholder="API Key dari penyedia pembayaran"
                value={formData.apiKey || ''}
                onChange={(e) => handleChange('apiKey', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="mode" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Mode <span className="text-red-500">*</span>
              </label>
              <Select
                id="mode"
                value={formData.mode || ''}
                onChange={(e) => handleChange('mode', e.target.value)}
                required
                className="w-full"
              >
                <option value="">Pilih Mode</option>
                <option value="test">Test</option>
                <option value="live">Live</option>
              </Select>
            </div>
          </>
        );
      
      case 'sso':
        return (
          <>
            <div className="mb-4">
              <label htmlFor="provider" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Penyedia SSO <span className="text-red-500">*</span>
              </label>
              <Select
                id="provider"
                value={formData.provider || ''}
                onChange={(e) => handleChange('provider', e.target.value)}
                required
                className="w-full"
              >
                <option value="">Pilih Penyedia</option>
                <option value="google">Google</option>
                <option value="microsoft">Microsoft Azure AD</option>
                <option value="okta">Okta</option>
                <option value="auth0">Auth0</option>
              </Select>
            </div>
            <div className="mb-4">
              <label htmlFor="clientId" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Client ID <span className="text-red-500">*</span>
              </label>
              <Input
                id="clientId"
                type="text"
                placeholder="Client ID dari penyedia SSO"
                value={formData.clientId || ''}
                onChange={(e) => handleChange('clientId', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="clientSecret" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Client Secret <span className="text-red-500">*</span>
              </label>
              <Input
                id="clientSecret"
                type="password"
                placeholder="Client Secret dari penyedia SSO"
                value={formData.clientSecret || ''}
                onChange={(e) => handleChange('clientSecret', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="domain" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Domain <span className="text-red-500">*</span>
              </label>
              <Input
                id="domain"
                type="text"
                placeholder="contoh: perusahaan.com"
                value={formData.domain || ''}
                onChange={(e) => handleChange('domain', e.target.value)}
                required
                className="w-full"
              />
            </div>
          </>
        );
      
      case 'third-party':
        return (
          <>
            <div className="mb-4">
              <label htmlFor="service" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Layanan <span className="text-red-500">*</span>
              </label>
              <Select
                id="service"
                value={formData.service || ''}
                onChange={(e) => handleChange('service', e.target.value)}
                required
                className="w-full"
              >
                <option value="">Pilih Layanan</option>
                <option value="google-analytics">Google Analytics</option>
                <option value="mailchimp">Mailchimp</option>
                <option value="slack">Slack</option>
                <option value="discord">Discord</option>
                <option value="other">Lainnya</option>
              </Select>
            </div>
            {formData.service === 'other' && (
              <div className="mb-4">
                <label htmlFor="customService" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                  Nama Layanan <span className="text-red-500">*</span>
                </label>
                <Input
                  id="customService"
                  type="text"
                  placeholder="Masukkan nama layanan"
                  value={formData.customService || ''}
                  onChange={(e) => handleChange('customService', e.target.value)}
                  required
                  className="w-full"
                />
              </div>
            )}
            <div className="mb-4">
              <label htmlFor="apiKey" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                API Key / Token <span className="text-red-500">*</span>
              </label>
              <Input
                id="apiKey"
                type="password"
                placeholder="API Key atau Token dari layanan"
                value={formData.apiKey || ''}
                onChange={(e) => handleChange('apiKey', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="category" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Kategori
              </label>
              <Select
                id="category"
                value={formData.category || ''}
                onChange={(e) => handleChange('category', e.target.value)}
                className="w-full"
              >
                <option value="">Pilih Kategori</option>
                <option value="analytics">Analytics</option>
                <option value="email">Email</option>
                <option value="communication">Komunikasi</option>
                <option value="marketing">Marketing</option>
                <option value="other">Lainnya</option>
              </Select>
            </div>
          </>
        );
      
      case 'webhook':
        return (
          <>
            <div className="mb-4">
              <label htmlFor="name" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Nama Webhook <span className="text-red-500">*</span>
              </label>
              <Input
                id="name"
                type="text"
                placeholder="Contoh: Notifikasi Kursus Selesai"
                value={formData.name || ''}
                onChange={(e) => handleChange('name', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="url" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                URL <span className="text-red-500">*</span>
              </label>
              <Input
                id="url"
                type="url"
                placeholder="https://example.com/webhook"
                value={formData.url || ''}
                onChange={(e) => handleChange('url', e.target.value)}
                required
                className="w-full"
              />
            </div>
            <div className="mb-4">
              <label htmlFor="event" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Event <span className="text-red-500">*</span>
              </label>
              <Select
                id="event"
                value={formData.event || ''}
                onChange={(e) => handleChange('event', e.target.value)}
                required
                className="w-full"
              >
                <option value="">Pilih Event</option>
                <option value="course.completed">Kursus Selesai</option>
                <option value="payment.success">Pembayaran Berhasil</option>
                <option value="user.created">Pengguna Baru</option>
                <option value="certificate.issued">Sertifikat Diterbitkan</option>
              </Select>
            </div>
            <div className="mb-4">
              <label htmlFor="secret" className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Secret Key
              </label>
              <Input
                id="secret"
                type="password"
                placeholder="Secret key untuk verifikasi webhook"
                value={formData.secret || ''}
                onChange={(e) => handleChange('secret', e.target.value)}
                className="w-full"
              />
            </div>
          </>
        );
      
      default:
        return null;
    }
  };

  const getModalTitle = () => {
    switch (integrationType) {
      case 'api': return 'Tambah API Key Baru';
      case 'payment': return 'Tambah Gateway Pembayaran';
      case 'sso': return 'Tambah Provider SSO';
      case 'third-party': return 'Tambah Integrasi Pihak Ketiga';
      case 'webhook': return 'Tambah Webhook Baru';
      default: return 'Tambah Integrasi';
    }
  };

  return (
    <div className="fixed inset-0 z-50 overflow-y-auto">
      <div className="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
        <div className="fixed inset-0 transition-opacity" aria-hidden="true">
          <div className="absolute inset-0 bg-gray-500 opacity-75 dark:bg-gray-900 dark:opacity-90"></div>
        </div>

        <span className="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>

        <div className="inline-block align-bottom bg-white dark:bg-gray-800 rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
          <div className="flex items-center justify-between px-6 pt-5 pb-2">
            <Heading level="h3" className="text-lg font-medium">{getModalTitle()}</Heading>
            <button
              type="button"
              className="text-gray-400 hover:text-gray-500 focus:outline-none"
              onClick={onClose}
            >
              <X className="h-6 w-6" />
            </button>
          </div>
          
          <div className="px-6 pb-6">
            <Text className="text-sm text-gray-500 dark:text-gray-400 mb-4">
              Isi formulir berikut untuk menambahkan integrasi baru.
            </Text>
            
            <form onSubmit={handleSubmit}>
              {getFormFields()}
              
              <div className="flex justify-end space-x-3 mt-6">
                <Button
                  type="button"
                  variant="secondary"
                  onClick={onClose}
                  className="border-gray-300 dark:border-gray-600"
                >
                  Batal
                </Button>
                <Button
                  type="submit"
                  variant="primary"
                  className="bg-brand-green hover:bg-brand-green/90"
                  disabled={isLoading}
                >
                  {isLoading ? 'Menyimpan...' : 'Simpan'}
                </Button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  );
};

export default AddIntegrationModal;

/== molecules/AffiliateTable.tsx
import React from "react";
import Badge from "../atoms/Badge";
import IconButton from "../atoms/IconButton";
import Text from "../atoms/Text";
import { Eye, Edit, Trash } from "lucide-react";

interface AffiliateProgram {
  id: string;
  name: string;
  referralCode: string;
  commission: string;
  registrations: number;
  status: "active" | "inactive" | "pending";
  earnings: number;
  conversions: number;
}

interface AffiliateTableProps {
  affiliates: AffiliateProgram[];
  onEdit: (id: string) => void;
  onView: (id: string) => void;
  onDelete: (id: string) => void;
}

const AffiliateTable: React.FC<AffiliateTableProps> = ({ 
  affiliates, 
  onEdit, 
  onView,
  onDelete 
}) => {
  return (
    <div className="overflow-x-auto w-full">
      <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
        <thead className="bg-gray-50 dark:bg-gray-800">
          <tr>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Nama Program
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Kode Referral
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Komisi
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Pendaftaran
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Konversi
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Total Komisi
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Status
            </th>
            <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
              Aksi
            </th>
          </tr>
        </thead>
        <tbody className="bg-white dark:bg-gray-900 divide-y divide-gray-200 dark:divide-gray-700">
          {affiliates.map((affiliate) => (
            <tr key={affiliate.id} className="hover:bg-gray-50 dark:hover:bg-gray-800">
              <td className="px-6 py-4 whitespace-nowrap">
                <Text className="font-medium text-gray-900 dark:text-white">{affiliate.name}</Text>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Badge color="blue">{affiliate.referralCode}</Badge>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Text className="text-gray-700 dark:text-gray-300">{affiliate.commission}</Text>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Text className="text-gray-700 dark:text-gray-300">{affiliate.registrations}</Text>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Text className="text-gray-700 dark:text-gray-300">{affiliate.conversions}</Text>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Text className="font-medium text-gray-900 dark:text-white">
                  Rp {affiliate.earnings.toLocaleString()}
                </Text>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <Badge 
                  color={affiliate.status === 'active' ? 'green' : 
                        affiliate.status === 'pending' ? 'yellow' : 'red'}
                >
                  {affiliate.status === 'active' ? 'Aktif' : 
                   affiliate.status === 'pending' ? 'Menunggu' : 'Tidak Aktif'}
                </Badge>
              </td>
              <td className="px-6 py-4 whitespace-nowrap">
                <div className="flex space-x-2">
                  <IconButton
                    icon={Eye}
                    variant="outline"
                    onClick={() => onView(affiliate.id)}
                    title="Lihat Detail"
                    size="sm"
                  />
                  <IconButton
                    icon={Edit}
                    variant="outline"
                    onClick={() => onEdit(affiliate.id)}
                    title="Edit Program"
                    size="sm"
                  />
                  <IconButton
                    icon={Trash}
                    variant="outline"
                    onClick={() => onDelete(affiliate.id)}
                    title="Hapus Program"
                    className="text-red-600 hover:text-red-700 hover:border-red-600"
                    size="sm"
                  />
                </div>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};

export default AffiliateTable;

/== molecules/AuthForm.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import AuthInput from '../atoms/AuthInput';
import Button from '../atoms/Button';

interface AuthFormProps {
  isLogin?: boolean;
  onSubmit: (e: React.FormEvent) => void;
  isLoading: boolean;
  error?: string;
  email: string;
  setEmail: (value: string) => void;
  password: string;
  setPassword: (value: string) => void;
  name?: string;
  setName?: (value: string) => void;
}

const AuthForm: React.FC<AuthFormProps> = ({
  isLogin = true,
  onSubmit,
  isLoading,
  error,
  email,
  setEmail,
  password,
  setPassword,
  name,
  setName
}) => {
  return (
    <form onSubmit={onSubmit} className="space-y-6">
      {error && (
        <div className="p-4 rounded-lg bg-red-50 dark:bg-red-900/50 border border-red-200 dark:border-red-800">
          <p className="text-sm text-red-600 dark:text-red-400">{error}</p>
        </div>
      )}

      {!isLogin && setName && (
        <AuthInput
          label="Full Name"
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
          required
          placeholder="Enter your full name"
        />
      )}

      <AuthInput
        label="Email Address"
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        required
        placeholder="Enter your email"
      />

      <AuthInput
        label="Password"
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        required
        placeholder="Enter your password"
      />

      <Button
        type="submit"
        variant="primary"
        className="w-full bg-brand-green hover:bg-brand-green/90"
        disabled={isLoading}
      >
        {isLoading ? 'Processing...' : isLogin ? 'Sign In' : 'Create Account'}
      </Button>

      <div className="text-center">
        <Link
          to={isLogin ? '/register' : '/login'}
          className="text-sm text-brand-green hover:text-brand-green/90"
        >
          {isLogin
            ? "Don't have an account? Sign up"
            : "Already have an account? Sign in"}
        </Link>
      </div>

      {isLogin && (
        <div className="text-center space-y-2">
          <p className="text-xs text-gray-500 dark:text-gray-400">
            Demo credentials (User): unpam@example.com / password123
          </p>
          <p className="text-xs text-gray-500 dark:text-gray-400">
            Demo credentials (Admin): admin@example.com / admin123
          </p>
        </div>
      )}
    </form>
  );
};

export default AuthForm;

/== molecules/AuthHeader.tsx
import React from 'react';
import { Link } from 'react-router-dom';

interface AuthHeaderProps {
  title: string;
  subtitle: string;
}

const AuthHeader: React.FC<AuthHeaderProps> = ({ title, subtitle }) => {
  return (
    <div className="text-center mb-8">
      <Link to="/" className="inline-block mb-6">
        <div className="flex items-center justify-center">
          <span className="text-3xl font-bold">CG</span>
          <span className="w-2 h-2 bg-brand-green rounded-full ml-1" />
        </div>
      </Link>
      <h1 className="text-3xl font-bold text-gray-900 dark:text-white mb-2">
        {title}
      </h1>
      <p className="text-gray-600 dark:text-gray-400">
        {subtitle}
      </p>
    </div>
  );
};

export default AuthHeader;

/== molecules/Breadcrumbs.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { ChevronRight } from 'lucide-react';

interface BreadcrumbItem {
  label: string;
  href?: string;
}

interface BreadcrumbsProps {
  items: BreadcrumbItem[];
}

const Breadcrumbs: React.FC<BreadcrumbsProps> = ({ items }) => {
  return (
    <nav className="flex" aria-label="Breadcrumb">
      <ol className="flex items-center space-x-2">
        {items.map((item, index) => (
          <li key={index} className="flex items-center">
            {index > 0 && (
              <ChevronRight className="h-4 w-4 text-gray-400 mx-2 flex-shrink-0" />
            )}
            {item.href ? (
              <Link
                to={item.href}
                className="text-sm font-medium text-gray-500 hover:text-gray-700"
              >
                {item.label}
              </Link>
            ) : (
              <span className="text-sm font-medium text-gray-900">
                {item.label}
              </span>
            )}
          </li>
        ))}
      </ol>
    </nav>
  );
};

export default Breadcrumbs;

/== molecules/BundleCard.tsx
import React from 'react';
import { Bundle } from '../../store/useBundleStore';
import  Badge  from '../atoms/Badge';
import Button  from '../atoms/Button';

interface BundleCardProps {
  bundle: Bundle;
  onEdit?: () => void;
  onDelete?: () => void;
  onView?: () => void;
}

export const BundleCard: React.FC<BundleCardProps> = ({
  bundle,
  onEdit,
  onDelete,
  onView,
}) => {
  const {
    title,
    description,
    originalPrice,
    bundlePrice,
    discountPercentage,
    courses,
    theme,
    difficultyLevel,
    limitedTime,
    startDate,
    endDate,
    isActive,
  } = bundle;
  
  return (
    <div className="border rounded-lg overflow-hidden shadow-sm bg-white">
      <div className="p-4 border-b">
        <div className="flex justify-between">
          <div>
            <h3 className="text-lg font-semibold text-gray-800">{title}</h3>
            <p className="text-sm text-gray-500 mb-2">{courses.length} courses</p>
          </div>
          <div className="text-right">
            <div className="text-lg font-bold text-blue-600">${bundlePrice.toFixed(2)}</div>
            <div className="text-sm text-gray-500 line-through">${originalPrice.toFixed(2)}</div>
            <Badge variant="success">Save {discountPercentage}%</Badge>
          </div>
        </div>
        <p className="text-sm text-gray-600 mt-2 line-clamp-2">{description}</p>
      </div>
      <div className="p-4 bg-gray-50">
        <div className="flex justify-between items-center mb-3">
          <div className="flex space-x-2">
            <Badge variant="info">{theme}</Badge>
            <Badge variant={
              difficultyLevel === 'beginner' ? 'info' : 
              difficultyLevel === 'intermediate' ? 'warning' : 'danger'
            }>
              {difficultyLevel}
            </Badge>
            {limitedTime && (
              <Badge variant="warning">Limited Time</Badge>
            )}
          </div>
          <div>
            {isActive ? (
              <Badge variant="success">Active</Badge>
            ) : (
              <Badge variant="danger">Inactive</Badge>
            )}
          </div>
        </div>
        {limitedTime && startDate && endDate && (
          <div className="text-xs text-gray-500 mb-3">
            Available from {new Date(startDate).toLocaleDateString()} to {new Date(endDate).toLocaleDateString()}
          </div>
        )}
        <div className="flex space-x-2">
          <Button size="sm" variant="outline" onClick={onView}>View</Button>
          <Button size="sm" variant="primary" onClick={onEdit}>Edit</Button>
          <Button size="sm" variant="danger" onClick={onDelete}>Delete</Button>
        </div>
      </div>
    </div>
  );
};

/== molecules/CodeEditor.tsx
import React, { useRef } from 'react';
import Editor from '@monaco-editor/react';

interface CodeEditorProps {
  code: string;
  onChange: (value: string | undefined) => void;
  language?: string;
  theme?: string;
  readOnly?: boolean;
  fontSize?: string;
  height?: string;
}

const CodeEditor: React.FC<CodeEditorProps> = ({
  code,
  onChange,
  language = 'python',
  theme = 'vs-dark',
  readOnly = false,
  fontSize = '14px',
  height = '100%'
}) => {
  const editorRef = useRef<any>(null);

  const handleEditorDidMount = (editor: any) => {
    editorRef.current = editor;
    editor.focus();
  };

  const options = {
    selectOnLineNumbers: true,
    roundedSelection: false,
    readOnly: readOnly,
    cursorStyle: 'line',
    automaticLayout: true,
    minimap: {
      enabled: false
    },
    scrollBeyondLastLine: false,
    fontSize: parseInt(fontSize),
    fontFamily: 'Menlo, Monaco, "Courier New", monospace',
    lineNumbers: 'on',
    folding: true,
    lineHeight: 21,
    renderLineHighlight: 'all',
    scrollbar: {
      useShadows: false,
      verticalScrollbarSize: 10,
      horizontalScrollbarSize: 10
    },
    overviewRulerLanes: 0,
    hideCursorInOverviewRuler: true,
    overviewRulerBorder: false
  };

  return (
    <div className="h-full w-full relative">
      <Editor
        height={height}
        language={language}
        theme={theme}
        value={code}
        options={options}
        onChange={onChange}
        onMount={handleEditorDidMount}
        loading={
          <div className="absolute inset-0 flex items-center justify-center bg-gray-900">
            <div className="text-white">Loading editor...</div>
          </div>
        }
      />
    </div>
  );
};

export default CodeEditor;

/== molecules/CourseCard.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Star, Clock, Users, Code } from 'lucide-react';
import Button from '../atoms/Button';

interface CourseCardProps {
  id: string;
  title: string;
  description: string;
  image: string;
  rating: number;
  students: number;
  duration: string;
  level: string;
  color: string;
}

const CourseCard: React.FC<CourseCardProps> = ({
  id,
  title,
  description,
  image,
  rating,
  students,
  duration,
  level,
  color
}) => {
  return (
    <div className="bg-white rounded-lg shadow-md overflow-hidden">
      <div className="relative">
        <img src={image} alt={title} className="w-full h-48 object-cover" />
        <div className="absolute top-0 right-0 bg-white px-3 py-1 m-2 rounded-full flex items-center">
          <Star className="h-4 w-4 text-yellow-400 mr-1" fill="currentColor" />
          <span className="text-sm font-medium">{rating}</span>
        </div>
      </div>
      
      <div className="p-6">
        <h3 className="text-xl font-bold text-gray-900 mb-2">{title}</h3>
        <p className="text-gray-600 mb-4">{description}</p>
        
        <div className="flex flex-wrap gap-4 mb-4">
          <div className="flex items-center text-sm text-gray-500">
            <Clock className="h-4 w-4 mr-1" />
            {duration}
          </div>
          <div className="flex items-center text-sm text-gray-500">
            <Users className="h-4 w-4 mr-1" />
            {(students || 0).toLocaleString()} students
          </div>
          <div className="flex items-center text-sm text-gray-500">
            <Code className="h-4 w-4 mr-1" />
            {level}
          </div>
        </div>
        
        <Link
          to={`/courses/${id}`}
          className={`inline-block w-full text-center px-4 py-2 ${color} text-white font-medium rounded hover:opacity-90 transition duration-300`}
        >
          Enroll Now
        </Link>
      </div>
    </div>
  );
};

export default CourseCard;

/== molecules/CourseSortable.tsx
import React from 'react';
import { Course } from '../../store/useBundleStore';

interface CourseSortableProps {
  courses: Course[];
  selectedCourseIds: string[];
  onReorder: (newOrder: string[]) => void;
}

export const CourseSortable: React.FC<CourseSortableProps> = ({
  courses,
  selectedCourseIds,
  onReorder,
}) => {
  const selectedCourses = courses.filter(course => 
    selectedCourseIds.includes(course.id)
  );

  const handleDragStart = (e: React.DragEvent, index: number) => {
    e.dataTransfer.setData('text/plain', index.toString());
  };

  const handleDragOver = (e: React.DragEvent) => {
    e.preventDefault();
  };

  const handleDrop = (e: React.DragEvent, dropIndex: number) => {
    const dragIndex = parseInt(e.dataTransfer.getData('text/plain'));
    const newOrder = [...selectedCourseIds];
    const item = newOrder[dragIndex];

    // Remove the item from its original position
    newOrder.splice(dragIndex, 1);
    // Insert it at the new position
    newOrder.splice(dropIndex, 0, item);

    onReorder(newOrder);
  };

  return (
    <div className="border rounded-lg overflow-hidden">
      <div className="bg-gray-50 p-3 border-b">
        <h3 className="font-medium">Course Order (Drag to Reorder)</h3>
      </div>
      <ul className="divide-y divide-gray-200">
        {selectedCourses.map((course, index) => (
          <li
            key={course.id}
            draggable
            onDragStart={(e) => handleDragStart(e, index)}
            onDragOver={handleDragOver}
            onDrop={(e) => handleDrop(e, index)}
            className="p-3 flex items-center bg-white hover:bg-gray-50 cursor-move"
          >
            <div className="mr-3 text-gray-500">
              <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M4 6h16M4 12h16M4 18h16" />
              </svg>
            </div>
            <div className="flex-1">
              <div className="font-medium">{course.title}</div>
              <div className="text-sm text-gray-500">{course.level} · {course.duration} hours</div>
            </div>
            <div className="text-gray-500 text-sm">
              ${course.price.toFixed(2)}
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
};

/== molecules/CriteriaItem.tsx
import React from 'react';
import { CheckCircle } from 'lucide-react';

interface CriteriaItemProps {
  title: string;
  description: string;
  points: number;
}

const CriteriaItem: React.FC<CriteriaItemProps> = ({
  title,
  description,
  points
}) => {
  return (
    <div className="flex items-start space-x-4 p-4 rounded-lg bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700">
      <div className="flex-shrink-0">
        <CheckCircle className="h-6 w-6 text-green-500" />
      </div>
      <div className="flex-1">
        <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-1">
          {title}
        </h3>
        <p className="text-gray-600 dark:text-gray-300 text-sm">
          {description}
        </p>
      </div>
      <div className="flex-shrink-0">
        <span className="inline-flex items-center px-3 py-1 rounded-full text-sm font-medium bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200">
          {points} points
        </span>
      </div>
    </div>
  );
};

export default CriteriaItem;

/== molecules/EmptyState.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';
import Button from '../atoms/Button';
import Text from '../atoms/Text';

interface EmptyStateProps {
  icon?: LucideIcon;
  title: string;
  description?: string;
  action?: {
    label: string;
    onClick: () => void;
  };
}

const EmptyState: React.FC<EmptyStateProps> = ({
  icon: Icon,
  title,
  description,
  action
}) => {
  return (
    <div className="flex flex-col items-center justify-center p-8 text-center">
      {Icon && (
        <div className="w-12 h-12 bg-gray-100 rounded-full flex items-center justify-center mb-4">
          <Icon className="h-6 w-6 text-gray-600" />
        </div>
      )}
      <Text variant="body" className="font-medium text-gray-900 mb-2">
        {title}
      </Text>
      {description && (
        <Text variant="caption" color="muted" className="mb-4">
          {description}
        </Text>
      )}
      {action && (
        <Button variant="primary" onClick={action.onClick}>
          {action.label}
        </Button>
      )}
    </div>
  );
};

export default EmptyState;

/== molecules/FeatureCard.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';
import Card from '../atoms/Card';

interface FeatureCardProps {
  icon: LucideIcon;
  title: string;
  description: string;
  color: string;
}

const FeatureCard: React.FC<FeatureCardProps> = ({
  icon: Icon,
  title,
  description,
  color
}) => {
  return (
    <Card className="p-8 hover:shadow-lg transition-shadow duration-300 bg-white dark:bg-gray-800">
      <div className={`${color} w-12 h-12 rounded-lg flex items-center justify-center mb-6`}>
        <Icon className="h-6 w-6 text-white" />
      </div>
      <h3 className="text-xl font-bold text-gray-900 dark:text-white mb-4">{title}</h3>
      <p className="text-gray-600 dark:text-gray-300 leading-relaxed">{description}</p>
    </Card>
  );
};

export default FeatureCard;

/== molecules/FormSection.tsx
import React, { ReactNode } from "react";
import Heading from "../atoms/Heading";
import Text from "../atoms/Text";

interface FormSectionProps {
  title: string;
  children: ReactNode;
  description?: string;
}

export const FormSection: React.FC<FormSectionProps> = ({ 
  title, 
  children, 
  description 
}) => {
  return (
    <div className="space-y-4">
      <div>
        <Heading level={4} className="mb-1 text-gray-900 dark:text-white">
          {title}
        </Heading>
        {description && (
          <Text className="text-sm text-gray-500 dark:text-gray-400">
            {description}
          </Text>
        )}
      </div>
      <div className="space-y-4">
        {children}
      </div>
    </div>
  );
};

// Also providing a default export as a backup option
export default FormSection;

/== molecules/IconText.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';

interface IconTextProps {
  icon: LucideIcon;
  text: string;
  className?: string;
}

const IconText: React.FC<IconTextProps> = ({
  icon: Icon,
  text,
  className = ''
}) => {
  return (
    <div className={`flex items-center ${className}`}>
      <Icon className="h-5 w-5 mr-2" />
      <span>{text}</span>
    </div>
  );
};

export default IconText;

/== molecules/IntegrationConfigForm.tsx
import React from 'react';
import Button from '../atoms/Button';
import Input from '../atoms/Input';
import TextArea from '../atoms/TextArea';
import Select from '../atoms/Select';
import Card from '../atoms/Card';
import Heading from '../atoms/Heading';
import Text from '../atoms/Text';

interface FormField {
  id: string;
  label: string;
  type: 'text' | 'password' | 'textarea' | 'select';
  placeholder?: string;
  options?: { value: string; label: string }[];
  required?: boolean;
  description?: string;
}

interface IntegrationConfigFormProps {
  title: string;
  description: string;
  fields: FormField[];
  onSubmit: (data: Record<string, any>) => void;
  submitLabel?: string;
  isLoading?: boolean;
  icon?: React.ReactNode;
}

const IntegrationConfigForm: React.FC<IntegrationConfigFormProps> = ({
  title,
  description,
  fields,
  onSubmit,
  submitLabel = 'Simpan Konfigurasi',
  isLoading = false,
  icon
}) => {
  const [formData, setFormData] = React.useState<Record<string, any>>({});

  const handleChange = (id: string, value: any) => {
    setFormData(prev => ({
      ...prev,
      [id]: value
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit(formData);
  };

  return (
    <Card className="p-6">
      <div className="flex items-center mb-4">
        {icon && <div className="p-2 rounded-full bg-brand-green/10 text-brand-green mr-3">{icon}</div>}
        <div>
          <Heading level="h2" className="text-xl font-semibold">{title}</Heading>
          <Text className="text-gray-600 dark:text-gray-400">{description}</Text>
        </div>
      </div>

      <form onSubmit={handleSubmit} className="space-y-4">
        {fields.map((field) => (
          <div key={field.id} className="space-y-2">
            <label htmlFor={field.id} className="block text-sm font-medium text-gray-700 dark:text-gray-300">
              {field.label}
              {field.required && <span className="text-red-500 ml-1">*</span>}
            </label>
            
            {field.description && (
              <Text className="text-xs text-gray-500 dark:text-gray-400 mb-1">{field.description}</Text>
            )}
            
            {field.type === 'textarea' ? (
              <TextArea
                id={field.id}
                placeholder={field.placeholder}
                value={formData[field.id] || ''}
                onChange={(e) => handleChange(field.id, e.target.value)}
                required={field.required}
                className="w-full"
              />
            ) : field.type === 'select' ? (
              <Select
                id={field.id}
                value={formData[field.id] || ''}
                onChange={(e) => handleChange(field.id, e.target.value)}
                required={field.required}
                className="w-full"
              >
                <option value="">Pilih {field.label}</option>
                {field.options?.map((option) => (
                  <option key={option.value} value={option.value}>
                    {option.label}
                  </option>
                ))}
              </Select>
            ) : (
              <Input
                id={field.id}
                type={field.type}
                placeholder={field.placeholder}
                value={formData[field.id] || ''}
                onChange={(e) => handleChange(field.id, e.target.value)}
                required={field.required}
                className="w-full"
              />
            )}
          </div>
        ))}

        <div className="pt-4">
          <Button
            type="submit"
            variant="primary"
            className="bg-brand-green hover:bg-brand-green/90 w-full sm:w-auto"
            disabled={isLoading}
          >
            {isLoading ? 'Menyimpan...' : submitLabel}
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default IntegrationConfigForm;

/== molecules/IntegrationForm.tsx
import React from 'react';
import Button from '../atoms/Button';

interface FormField {
  id: string;
  label: string;
  type: string;
  placeholder?: string;
  required?: boolean;
  options?: { value: string; label: string }[];
}

interface IntegrationFormProps {
  fields: FormField[];
  values: Record<string, any>;
  onChange: (id: string, value: any) => void;
  onSubmit: (e: React.FormEvent) => void;
  isSubmitting?: boolean;
  submitText?: string;
  cancelText?: string;
  onCancel?: () => void;
}

const IntegrationForm: React.FC<IntegrationFormProps> = ({
  fields,
  values,
  onChange,
  onSubmit,
  isSubmitting = false,
  submitText = 'Simpan',
  cancelText = 'Batal',
  onCancel
}) => {
  const handleChange = (id: string, e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement | HTMLTextAreaElement>) => {
    onChange(id, e.target.value);
  };

  const renderField = (field: FormField) => {
    const { id, label, type, placeholder, required, options } = field;
    
    switch (type) {
      case 'select':
        return (
          <div key={id} className="mb-4">
            <label htmlFor={id} className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              {label}{required && <span className="text-red-500 ml-1">*</span>}
            </label>
            <select
              id={id}
              value={values[id] || ''}
              onChange={(e) => handleChange(id, e)}
              className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              required={required}
            >
              <option value="">Pilih {label}</option>
              {options?.map((option) => (
                <option key={option.value} value={option.value}>
                  {option.label}
                </option>
              ))}
            </select>
          </div>
        );
      
      case 'textarea':
        return (
          <div key={id} className="mb-4">
            <label htmlFor={id} className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              {label}{required && <span className="text-red-500 ml-1">*</span>}
            </label>
            <textarea
              id={id}
              value={values[id] || ''}
              onChange={(e) => handleChange(id, e)}
              placeholder={placeholder}
              className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              required={required}
              rows={4}
            />
          </div>
        );
      
      case 'checkbox':
        return (
          <div key={id} className="mb-4 flex items-center">
            <input
              id={id}
              type="checkbox"
              checked={values[id] || false}
              onChange={(e) => onChange(id, e.target.checked)}
              className="h-4 w-4 text-brand-green focus:ring-brand-green border-gray-300 rounded"
            />
            <label htmlFor={id} className="ml-2 block text-sm text-gray-700 dark:text-gray-300">
              {label}
            </label>
          </div>
        );
      
      default: // text, email, password, etc.
        return (
          <div key={id} className="mb-4">
            <label htmlFor={id} className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
              {label}{required && <span className="text-red-500 ml-1">*</span>}
            </label>
            <input
              id={id}
              type={type}
              value={values[id] || ''}
              onChange={(e) => handleChange(id, e)}
              placeholder={placeholder}
              className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-brand-green dark:bg-gray-700 dark:text-white"
              required={required}
            />
          </div>
        );
    }
  };

  return (
    <form onSubmit={onSubmit}>
      {fields.map(renderField)}
      
      <div className="flex justify-end space-x-3 mt-6">
        {onCancel && (
          <Button 
            variant="outline" 
            onClick={onCancel} 
            type="button"
          >
            {cancelText}
          </Button>
        )}
        <Button 
          variant="primary" 
          type="submit" 
          disabled={isSubmitting}
          className="bg-brand-green hover:bg-brand-green/90"
        >
          {isSubmitting ? 'Menyimpan...' : submitText}
        </Button>
      </div>
    </form>
  );
};

export default IntegrationForm;

/== molecules/LessonNavigation.tsx
import React from 'react';
import { ChevronLeft, ChevronRight } from 'lucide-react';
import Button from '../atoms/Button';

interface LessonNavigationProps {
  currentStep: number;
  totalSteps: number;
  onPrevious: () => void;
  onNext: () => void;
  isLastStep: boolean;
}

const LessonNavigation: React.FC<LessonNavigationProps> = ({
  currentStep,
  totalSteps,
  onPrevious,
  onNext,
  isLastStep
}) => {
  return (
    <div className="flex items-center justify-between">
      <Button
        variant="outline"
        icon={ChevronLeft}
        onClick={onPrevious}
        disabled={currentStep === 1}
      >
        Previous Step
      </Button>
      
      <Button
        variant="primary"
        icon={ChevronRight}
        onClick={onNext}
        iconPosition="right"
      >
        {isLastStep ? 'Complete Lesson' : 'Next Step'}
      </Button>
    </div>
  );
};

export default LessonNavigation;

/== molecules/LoadingSpinner.tsx
import React from 'react';
import Spinner from '../atoms/Spinner';
import Text from '../atoms/Text';

interface LoadingSpinnerProps {
  text?: string;
  size?: 'sm' | 'md' | 'lg';
  color?: 'primary' | 'white';
}

const LoadingSpinner: React.FC<LoadingSpinnerProps> = ({
  text,
  size = 'md',
  color = 'primary'
}) => {
  return (
    <div className="flex flex-col items-center justify-center">
      <Spinner size={size} color={color} />
      {text && (
        <Text
          variant="caption"
          color={color === 'white' ? 'default' : 'muted'}
          className="mt-2"
        >
          {text}
        </Text>
      )}
    </div>
  );
};

export default LoadingSpinner;

/== molecules/NotificationDropdown.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Bell, Check, Info, AlertTriangle, XCircle, X, CheckCheck } from 'lucide-react';
import { useNotificationStore, Notification } from '../../store/notificationStore';

interface NotificationDropdownProps {
  isOpen: boolean;
  onClose: () => void;
}

const NotificationDropdown: React.FC<NotificationDropdownProps> = ({
  isOpen,
  onClose
}) => {
  const { notifications, markAsRead, markAllAsRead, removeNotification } = useNotificationStore();

  if (!isOpen) return null;

  const getIcon = (type: Notification['type']) => {
    switch (type) {
      case 'success':
        return <Check className="h-5 w-5 text-green-500" />;
      case 'warning':
        return <AlertTriangle className="h-5 w-5 text-yellow-500" />;
      case 'error':
        return <XCircle className="h-5 w-5 text-red-500" />;
      default:
        return <Info className="h-5 w-5 text-blue-500" />;
    }
  };

  const formatTimestamp = (timestamp: number) => {
    const now = Date.now();
    const diff = now - timestamp;
    const minutes = Math.floor(diff / 60000);
    const hours = Math.floor(minutes / 60);
    const days = Math.floor(hours / 24);

    if (days > 0) return `${days}d ago`;
    if (hours > 0) return `${hours}h ago`;
    if (minutes > 0) return `${minutes}m ago`;
    return 'Just now';
  };

  return (
    <div className="absolute right-0 mt-2 w-96 bg-white dark:bg-gray-800 rounded-lg shadow-lg py-1 ring-1 ring-black ring-opacity-5 z-50">
      <div className="px-4 py-2 border-b border-gray-200 dark:border-gray-700">
        <div className="flex items-center justify-between">
          <h3 className="text-lg font-semibold text-gray-900 dark:text-white">Notifications</h3>
          <button
            onClick={markAllAsRead}
            className="text-sm text-blue-600 dark:text-blue-400 hover:text-blue-800 dark:hover:text-blue-300 flex items-center"
          >
            <CheckCheck className="h-4 w-4 mr-1" />
            Mark all as read
          </button>
        </div>
      </div>

      <div className="max-h-96 overflow-y-auto">
        {notifications.length === 0 ? (
          <div className="px-4 py-6 text-center text-gray-500 dark:text-gray-400">
            No notifications
          </div>
        ) : (
          notifications.map((notification) => (
            <div
              key={notification.id}
              className={`
                px-4 py-3 hover:bg-gray-50 dark:hover:bg-gray-700/50 relative
                ${notification.read ? 'opacity-75' : 'bg-blue-50/50 dark:bg-blue-900/20'}
              `}
            >
              <div className="flex items-start">
                <div className="flex-shrink-0 mt-1">
                  {getIcon(notification.type)}
                </div>
                <div className="ml-3 flex-1">
                  <div className="text-sm font-medium text-gray-900 dark:text-white">
                    {notification.title}
                  </div>
                  <p className="mt-1 text-sm text-gray-600 dark:text-gray-300">
                    {notification.message}
                  </p>
                  <div className="mt-2 text-xs text-gray-500 dark:text-gray-400 flex items-center justify-between">
                    <span>{formatTimestamp(notification.timestamp)}</span>
                    {notification.link && (
                      <Link
                        to={notification.link}
                        className="text-blue-600 dark:text-blue-400 hover:text-blue-800 dark:hover:text-blue-300"
                        onClick={() => {
                          markAsRead(notification.id);
                          onClose();
                        }}
                      >
                        View details
                      </Link>
                    )}
                  </div>
                </div>
                <button
                  onClick={() => removeNotification(notification.id)}
                  className="ml-4 text-gray-400 hover:text-gray-600 dark:hover:text-gray-300"
                >
                  <X className="h-4 w-4" />
                </button>
              </div>
            </div>
          ))
        )}
      </div>
    </div>
  );
};

export default NotificationDropdown;

/== molecules/PaymentForm.tsx
import React from 'react';
import { CreditCard } from 'lucide-react';
import PaymentInput from '../atoms/PaymentInput';
import PaymentMethodButton from '../atoms/PaymentMethodButton';

interface PaymentFormProps {
  paymentMethod: 'card' | 'paypal';
  setPaymentMethod: (method: 'card' | 'paypal') => void;
  cardNumber: string;
  setCardNumber: (value: string) => void;
  cardName: string;
  setCardName: (value: string) => void;
  expiry: string;
  setExpiry: (value: string) => void;
  cvv: string;
  setCvv: (value: string) => void;
  onSubmit: (e: React.FormEvent) => void;
  isProcessing: boolean;
}

const PaymentForm: React.FC<PaymentFormProps> = ({
  paymentMethod,
  setPaymentMethod,
  cardNumber,
  setCardNumber,
  cardName,
  setCardName,
  expiry,
  setExpiry,
  cvv,
  setCvv,
  onSubmit,
  isProcessing
}) => {
  return (
    <form onSubmit={onSubmit} className="space-y-6">
      <div className="grid grid-cols-2 gap-4">
        <PaymentMethodButton
          icon={<img src="https://cdn.paddle.com/paypal/paypal@2x.png" alt="PayPal" className="h-6" />}
          name="PayPal"
          selected={paymentMethod === 'paypal'}
          onClick={() => setPaymentMethod('paypal')}
        />
        <PaymentMethodButton
          icon={<CreditCard className="h-5 w-5" />}
          name="Card"
          selected={paymentMethod === 'card'}
          onClick={() => setPaymentMethod('card')}
        />
      </div>

      {paymentMethod === 'card' ? (
        <div className="space-y-4">
          <PaymentInput
            label="Card number"
            type="text"
            value={cardNumber}
            onChange={(e) => setCardNumber(e.target.value)}
            placeholder="1234 5678 9012 3456"
            icon={<CreditCard className="h-5 w-5" />}
          />
          
          <PaymentInput
            label="Name on card"
            type="text"
            value={cardName}
            onChange={(e) => setCardName(e.target.value)}
            placeholder="John Doe"
          />
          
          <div className="grid grid-cols-2 gap-4">
            <PaymentInput
              label="Expiry date"
              type="text"
              value={expiry}
              onChange={(e) => setExpiry(e.target.value)}
              placeholder="MM/YY"
            />
            <PaymentInput
              label="Security code"
              type="text"
              value={cvv}
              onChange={(e) => setCvv(e.target.value)}
              placeholder="CVV"
            />
          </div>
        </div>
      ) : (
        <div className="bg-blue-50 p-4 rounded-lg">
          <p className="text-sm text-blue-600">
            You will be redirected to PayPal to complete your payment securely.
          </p>
        </div>
      )}

      <button
        type="submit"
        disabled={isProcessing}
        className={`
          w-full py-3 px-4 rounded-lg font-medium text-white
          ${isProcessing 
            ? 'bg-gray-400 cursor-not-allowed' 
            : 'bg-primary-500 hover:bg-primary-600'
          }
        `}
      >
        {isProcessing ? 'Processing...' : 'Subscribe now'}
      </button>
    </form>
  );
};

export default PaymentForm;

/== molecules/PaymentSummary.tsx
import React from 'react';
import { Info } from 'lucide-react';

interface PaymentSummaryProps {
  originalPrice: number;
  discount: number;
  finalPrice: number;
  plan: string;
  billingCycle: string;
}

const PaymentSummary: React.FC<PaymentSummaryProps> = ({
  originalPrice,
  discount,
  finalPrice,
  plan,
  billingCycle
}) => {
  return (
    <div className="bg-white/10 rounded-xl p-6">
      <div className="flex items-center justify-center text-blue-400 mb-4">
        <Info className="h-5 w-5 mr-2" />
        <span className="text-sm">14-day money back guarantee</span>
      </div>

      <h2 className="text-lg font-medium text-white mb-6">Review Payment Details</h2>
      
      <div className="space-y-4">
        <div className="flex justify-between text-gray-300">
          <span>{billingCycle === 'yearly' ? 'Yearly' : 'Monthly'} {plan} for 🇮🇩</span>
          <span>Rp {originalPrice.toLocaleString()}</span>
        </div>
        
        {discount > 0 && (
          <div className="flex justify-between">
            <span className="text-green-400">Annual Discount Applied: -66%</span>
            <span className="text-green-400">-Rp {discount.toLocaleString()}</span>
          </div>
        )}
        
        <div className="pt-4 border-t border-gray-700">
          <div className="flex justify-between text-white font-medium">
            <span>Total</span>
            <span>Rp {finalPrice.toLocaleString()}</span>
          </div>
        </div>
      </div>
    </div>
  );
};

export default PaymentSummary;

/== molecules/PricingCard.tsx
import React from 'react';
import { Check } from 'lucide-react';
import PricingFeature from '../atoms/PricingFeature';

interface PricingCardProps {
  name: string;
  description: string;
  price: number;
  period?: string;
  features: Array<{ name: string; included: boolean }>;
  popular?: boolean;
  onSubscribe: () => void;
  buttonText: string;
  buttonVariant?: 'primary' | 'secondary' | 'outline';
}

const PricingCard: React.FC<PricingCardProps> = ({
  name,
  description,
  price,
  period,
  features,
  popular,
  onSubscribe,
  buttonText,
  buttonVariant = 'primary'
}) => {
  const buttonStyles = {
    primary: 'bg-primary-500 text-white hover:bg-primary-600',
    secondary: 'bg-blue-600 text-white hover:bg-blue-700',
    outline: 'border border-gray-300 text-gray-700 hover:bg-gray-50'
  };

  return (
    <div className={`
      bg-white rounded-xl shadow-sm p-8
      ${popular ? 'border-2 border-primary-500 relative' : 'border border-gray-200'}
    `}>
      {popular && (
        <div className="absolute top-0 right-0 bg-primary-500 text-white px-4 py-1 rounded-bl-lg rounded-tr-lg text-sm font-medium">
          Most Popular
        </div>
      )}

      <h2 className="text-2xl font-bold text-gray-900 mb-2">{name}</h2>
      <p className="text-gray-600 mb-6">{description}</p>

      <div className="mb-8">
        <span className="text-4xl font-bold text-gray-900">
          Rp {price.toLocaleString()}
        </span>
        {period && (
          <span className="text-gray-600 ml-2">/ {period}</span>
        )}
      </div>

      <button
        onClick={onSubscribe}
        className={`w-full py-3 px-4 rounded-lg font-medium mb-8 ${buttonStyles[buttonVariant]}`}
      >
        {buttonText}
      </button>

      <ul className="space-y-4">
        {features.map((feature, index) => (
          <PricingFeature
            key={index}
            name={feature.name}
            included={feature.included}
          />
        ))}
      </ul>

      {popular && (
        <div className="mt-6 pt-6 border-t border-gray-100 text-center">
          <span className="text-sm text-gray-500">14-day money back guarantee</span>
        </div>
      )}
    </div>
  );
};

export default PricingCard;

/== molecules/ProgressBar.tsx
import React from 'react';
import Progress from '../atoms/Progress';
import Text from '../atoms/Text';

interface ProgressBarProps {
  value: number;
  max?: number;
  label?: string;
  showPercentage?: boolean;
  size?: 'sm' | 'md' | 'lg';
  color?: 'primary' | 'success' | 'warning' | 'error';
}

const ProgressBar: React.FC<ProgressBarProps> = ({
  value,
  max = 100,
  label,
  showPercentage = true,
  size = 'md',
  color = 'primary'
}) => {
  const percentage = Math.round((value / max) * 100);

  return (
    <div className="w-full">
      {(label || showPercentage) && (
        <div className="flex justify-between mb-1">
          {label && (
            <Text variant="caption" color="muted">{label}</Text>
          )}
          {showPercentage && (
            <Text variant="caption" color="muted">{percentage}%</Text>
          )}
        </div>
      )}
      <Progress
        value={value}
        max={max}
        size={size}
        color={color}
      />
    </div>
  );
};

export default ProgressBar;

/== molecules/ProgressStats.tsx
import React from 'react';

interface ProgressStatsProps {
  title: string;
  value: number;
  max: number;
  percentage: number;
  color?: string;
}

export const ProgressStats: React.FC<ProgressStatsProps> = ({
  title,
  value,
  max,
  percentage,
  color = 'blue',
}) => {
  const colorMap: Record<string, string> = {
    blue: 'bg-blue-600',
    green: 'bg-green-600',
    red: 'bg-red-600',
    yellow: 'bg-yellow-600',
    purple: 'bg-purple-600',
  };
  
  const bgColor = colorMap[color] || colorMap.blue;
  
  return (
    <div className="bg-white rounded-lg shadow p-4">
      <h4 className="text-sm font-medium text-gray-500 mb-1">{title}</h4>
      <div className="flex justify-between items-end mb-1">
        <span className="text-2xl font-bold">{value}</span>
        <span className="text-sm text-gray-500">of {max}</span>
      </div>
      <div className="w-full bg-gray-200 rounded-full h-2.5">
        <div
          className={`h-2.5 rounded-full ${bgColor}`}
          style={{ width: `${percentage}%` }}
        ></div>
      </div>
      <div className="text-sm text-right mt-1">{percentage}% Complete</div>
    </div>
  );
};

/== molecules/ResourceCard.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';
import Card from '../atoms/Card';
import Text from '../atoms/Text';
import Button from '../atoms/Button';

interface ResourceCardProps {
  icon: LucideIcon;
  title: string;
  description: string;
  link: string;
  linkText: string;
  bgColor: string;
}

const ResourceCard: React.FC<ResourceCardProps> = ({
  icon: Icon,
  title,
  description,
  link,
  linkText,
  bgColor
}) => {
  return (
    <Card className={`p-6 ${bgColor}`}>
      <div className="flex items-center mb-4">
        <Icon className="h-6 w-6 text-gray-700" />
        <Text variant="body" className="font-medium text-gray-900 ml-2">{title}</Text>
      </div>
      <Text variant="body" color="muted" className="mb-4">{description}</Text>
      <Button
        variant="primary"
        onClick={() => window.location.href = link}
      >
        {linkText}
      </Button>
    </Card>
  );
};

export default ResourceCard;

/== molecules/ReviewFeedback.tsx
import React from 'react';
import { AlertCircle, CheckCircle, FileText } from 'lucide-react';

interface FeedbackItem {
  type: 'success' | 'error';
  message: string;
}

interface ReviewFeedbackProps {
  feedback: FeedbackItem[];
  reviewerNotes?: string;
  submissionFiles?: {
    name: string;
    path: string;
    comments: number;
  }[];
}

const ReviewFeedback: React.FC<ReviewFeedbackProps> = ({
  feedback,
  reviewerNotes,
  submissionFiles
}) => {
  return (
    <div className="space-y-6">
      {/* Feedback Items */}
      <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
        <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-4">
          Review Results
        </h3>
        <div className="space-y-4">
          {feedback.map((item, index) => (
            <div
              key={index}
              className={`flex items-start p-4 rounded-lg ${
                item.type === 'success'
                  ? 'bg-green-50 dark:bg-green-900/20'
                  : 'bg-red-50 dark:bg-red-900/20'
              }`}
            >
              {item.type === 'success' ? (
                <CheckCircle className="h-5 w-5 text-green-500" />
              ) : (
                <AlertCircle className="h-5 w-5 text-red-500" />
              )}
              <p className={`ml-3 text-sm ${
                item.type === 'success'
                  ? 'text-green-700 dark:text-green-200'
                  : 'text-red-700 dark:text-red-200'
              }`}>
                {item.message}
              </p>
            </div>
          ))}
        </div>
      </div>

      {/* Reviewer Notes */}
      {reviewerNotes && (
        <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
          <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-4">
            Reviewer Notes
          </h3>
          <div className="prose prose-sm dark:prose-invert max-w-none">
            <p className="text-gray-600 dark:text-gray-300">{reviewerNotes}</p>
          </div>
        </div>
      )}

      {/* Submission Files */}
      {submissionFiles && submissionFiles.length > 0 && (
        <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
          <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-4">
            Submission Files
          </h3>
          <div className="space-y-2">
            {submissionFiles.map((file, index) => (
              <div
                key={index}
                className="flex items-center justify-between p-3 rounded-lg border border-gray-200 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-700/50"
              >
                <div className="flex items-center">
                  <FileText className="h-5 w-5 text-gray-400" />
                  <span className="ml-2 text-sm font-medium text-gray-900 dark:text-white">
                    {file.name}
                  </span>
                  {file.comments > 0 && (
                    <span className="ml-2 px-2 py-0.5 text-xs font-medium bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200 rounded-full">
                      {file.comments} comments
                    </span>
                  )}
                </div>
                <button className="text-sm text-blue-600 dark:text-blue-400 hover:underline">
                  View
                </button>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
};

export default ReviewFeedback;

/== molecules/RoadmapModal.tsx
import React from 'react';
import { X, ExternalLink, BookOpen, Code, CheckCircle } from 'lucide-react';
import Button from '../atoms/Button';

interface RoadmapModalProps {
  isOpen: boolean;
  onClose: () => void;
  node: {
    id: string;
    title: string;
    description?: string;
    type: string;
  } | null;
  onComplete?: () => void;
  isCompleted?: boolean;
}

const RoadmapModal: React.FC<RoadmapModalProps> = ({
  isOpen,
  onClose,
  node,
  onComplete,
  isCompleted = false
}) => {
  if (!isOpen || !node) return null;

  const resources = [
    {
      title: 'Official Documentation',
      description: 'Read the comprehensive guide and documentation',
      icon: BookOpen,
      link: '#'
    },
    {
      title: 'Practice Examples',
      description: 'Try hands-on coding examples and exercises',
      icon: Code,
      link: '#'
    }
  ];

  return (
    <div className="fixed inset-0 z-50 overflow-y-auto">
      <div className="flex min-h-screen items-center justify-center p-4">
        <div className="fixed inset-0 bg-black/50 backdrop-blur-sm" onClick={onClose} />
        
        <div className="relative w-full max-w-2xl rounded-xl bg-white dark:bg-gray-800 shadow-lg">
          {/* Header */}
          <div className="flex items-center justify-between p-6 border-b border-gray-200 dark:border-gray-700">
            <div>
              <h3 className="text-xl font-bold text-gray-900 dark:text-white">
                {node.title}
              </h3>
              <p className="mt-1 text-sm text-gray-500 dark:text-gray-400">
                {node.description || 'Learn more about this topic and access related resources.'}
              </p>
            </div>
            <button
              onClick={onClose}
              className="text-gray-400 hover:text-gray-500 dark:hover:text-gray-300"
            >
              <X className="h-5 w-5" />
            </button>
          </div>

          {/* Content */}
          <div className="p-6">
            <div className="space-y-6">
              {/* Status */}
              <div className={`flex items-center p-4 rounded-lg ${
                isCompleted ? 'bg-green-50 dark:bg-green-900/20' : 'bg-blue-50 dark:bg-blue-900/20'
              }`}>
                <div className={`w-10 h-10 rounded-full flex items-center justify-center ${
                  isCompleted ? 'bg-green-100 dark:bg-green-900/40' : 'bg-blue-100 dark:bg-blue-900/40'
                }`}>
                  {isCompleted ? (
                    <CheckCircle className="h-6 w-6 text-green-600 dark:text-green-400" />
                  ) : (
                    <BookOpen className="h-6 w-6 text-blue-600 dark:text-blue-400" />
                  )}
                </div>
                <div className="ml-4">
                  <h4 className={`font-medium ${
                    isCompleted ? 'text-green-800 dark:text-green-200' : 'text-blue-800 dark:text-blue-200'
                  }`}>
                    {isCompleted ? 'Completed' : 'Ready to Learn'}
                  </h4>
                  <p className={`text-sm ${
                    isCompleted ? 'text-green-600 dark:text-green-300' : 'text-blue-600 dark:text-blue-300'
                  }`}>
                    {isCompleted 
                      ? "You've mastered this topic! Want to review it again?"
                      : 'Start learning this topic with our curated resources'}
                  </p>
                </div>
              </div>

              {/* Resources */}
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {resources.map((resource, index) => (
                  <div
                    key={index}
                    className="p-4 rounded-lg border border-gray-200 dark:border-gray-700 hover:border-blue-500 dark:hover:border-blue-400 transition-colors duration-200"
                  >
                    <div className="flex items-start">
                      <div className="w-8 h-8 bg-blue-100 dark:bg-blue-900/40 rounded-lg flex items-center justify-center">
                        <resource.icon className="h-5 w-5 text-blue-600 dark:text-blue-400" />
                      </div>
                      <div className="ml-3">
                        <h5 className="text-sm font-medium text-gray-900 dark:text-white">
                          {resource.title}
                        </h5>
                        <p className="mt-1 text-xs text-gray-500 dark:text-gray-400">
                          {resource.description}
                        </p>
                      </div>
                    </div>
                    <a
                      href={resource.link}
                      className="mt-3 inline-flex items-center text-sm text-blue-600 dark:text-blue-400 hover:text-blue-800 dark:hover:text-blue-300"
                    >
                      Access Resource
                      <ExternalLink className="h-4 w-4 ml-1" />
                    </a>
                  </div>
                ))}
              </div>
            </div>
          </div>

          {/* Footer */}
          <div className="flex items-center justify-end gap-4 p-6 border-t border-gray-200 dark:border-gray-700">
            <Button
              variant="outline"
              onClick={onClose}
            >
              Close
            </Button>
            {!isCompleted && onComplete && (
              <Button
                variant="primary"
                onClick={onComplete}
              >
                Mark as Complete
              </Button>
            )}
          </div>
        </div>
      </div>
    </div>
  );
};

export default RoadmapModal;

/== molecules/RoadmapNodeComponent.tsx
import React from 'react';
import { motion } from 'framer-motion';
import { Check, ChevronRight } from 'lucide-react';
import { RoadmapNode } from '../../store/roadmapStore';

interface RoadmapNodeComponentProps {
  node: RoadmapNode;
  onClick: (node: RoadmapNode) => void;
  isActive: boolean;
  isCompleted: boolean;
}

const RoadmapNodeComponent: React.FC<RoadmapNodeComponentProps> = ({
  node,
  onClick,
  isActive,
  isCompleted
}) => {
  const getNodeStyles = () => {
    const baseStyles = "px-4 py-2 rounded-lg font-medium text-sm transition-all duration-200 cursor-pointer";
    
    if (isCompleted) {
      return `${baseStyles} bg-green-100 text-green-800 border-2 border-green-500`;
    }
    
    if (isActive) {
      return `${baseStyles} ring-2 ring-blue-400 bg-blue-50 text-blue-800`;
    }
    
    switch (node.type) {
      case 'primary':
        return `${baseStyles} bg-yellow-400 text-yellow-900 hover:bg-yellow-500`;
      case 'highlight':
        return `${baseStyles} bg-purple-600 text-white hover:bg-purple-700`;
      default:
        return `${baseStyles} bg-gray-100 text-gray-800 hover:bg-gray-200`;
    }
  };

  return (
    <motion.div
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -20 }}
      className={getNodeStyles()}
      onClick={() => onClick(node)}
      whileHover={{ scale: 1.02 }}
      whileTap={{ scale: 0.98 }}
    >
      <div className="flex items-center justify-between">
        <span>{node.title}</span>
        {isCompleted ? (
          <Check className="h-4 w-4 ml-2" />
        ) : (
          node.children && <ChevronRight className="h-4 w-4 ml-2" />
        )}
      </div>
    </motion.div>
  );
};

export default RoadmapNodeComponent;

/== molecules/RoadmapSection.tsx
import React from 'react';
import RoadmapNode from '../atoms/RoadmapNode';
import RoadmapConnection from '../atoms/RoadmapConnection';

interface RoadmapSectionProps {
  title: string;
  items: {
    id: string;
    title: string;
    isCompleted?: boolean;
    isActive?: boolean;
    type?: 'primary' | 'secondary' | 'highlight';
  }[];
  onItemClick?: (id: string) => void;
}

const RoadmapSection: React.FC<RoadmapSectionProps> = ({
  title,
  items,
  onItemClick
}) => {
  return (
    <div className="space-y-4">
      <h3 className="text-lg font-semibold text-gray-900 dark:text-white">
        {title}
      </h3>
      <div className="space-y-3">
        {items.map((item, index) => (
          <div key={item.id}>
            <RoadmapNode
              title={item.title}
              isCompleted={item.isCompleted}
              isActive={item.isActive}
              type={item.type}
              onClick={() => onItemClick?.(item.id)}
            />
            {index < items.length - 1 && (
              <RoadmapConnection className="my-2" />
            )}
          </div>
        ))}
      </div>
    </div>
  );
};

export default RoadmapSection;

/== molecules/SearchBar.tsx
import React from 'react';
import { Search } from 'lucide-react';
import Input from '../atoms/Input';

interface SearchBarProps {
  placeholder?: string;
  value: string;
  onChange: (value: string) => void;
}

const SearchBar: React.FC<SearchBarProps> = ({
  placeholder = 'Search...',
  value,
  onChange
}) => {
  return (
    <div className="relative">
      <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
        <Search className="h-5 w-5 text-gray-400" />
      </div>
      <Input
        type="text"
        placeholder={placeholder}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        className="pl-10 pr-3 py-2"
      />
    </div>
  );
};

export default SearchBar;

/== molecules/SearchInput.tsx
import React from 'react';
import { Search } from 'lucide-react';

interface SearchInputProps {
  value: string;
  onChange: (value: string) => void;
  placeholder?: string;
  className?: string;
}

const SearchInput: React.FC<SearchInputProps> = ({
  value,
  onChange,
  placeholder = 'Search...',
  className = ''
}) => {
  return (
    <div className={`relative ${className}`}>
      <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
        <Search className="h-5 w-5 text-gray-400" />
      </div>
      <input
        type="text"
        value={value}
        onChange={(e) => onChange(e.target.value)}
        className="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        placeholder={placeholder}
      />
    </div>
  );
};

export default SearchInput;

/== molecules/StatCard.tsx
import React from 'react';
import Card from '../atoms/Card';

interface StatCardProps {
  value: string | number;
  label: string;
  icon?: React.ReactNode;
}

const StatCard: React.FC<StatCardProps> = ({ value, label, icon }) => {
  return (
    <Card className="p-6 bg-white dark:bg-gray-800">
      <div className="flex items-center">
        {icon && <div className="mr-4">{icon}</div>}
        <div>
          <div className="text-3xl font-bold text-gray-900 dark:text-white mb-1">{value}</div>
          <div className="text-sm text-gray-600 dark:text-gray-300">{label}</div>
        </div>
      </div>
    </Card>
  );
};

export default StatCard;

/== molecules/StatsDisplay.tsx
import React from 'react';
import { DivideIcon as LucideIcon } from 'lucide-react';

interface StatsDisplayProps {
  icon: LucideIcon;
  label: string;
  value: string | number;
  color?: string;
}

const StatsDisplay: React.FC<StatsDisplayProps> = ({
  icon: Icon,
  label,
  value,
  color = 'blue'
}) => {
  return (
    <div className="flex items-center">
      <div className={`w-10 h-10 bg-${color}-100 rounded-full flex items-center justify-center mr-3`}>
        <Icon className={`h-5 w-5 text-${color}-600`} />
      </div>
      <div>
        <div className="font-medium text-gray-900">{value}</div>
        <div className="text-xs text-gray-500">{label}</div>
      </div>
    </div>
  );
};

export default StatsDisplay;

/== molecules/StepIndicator.tsx
import React from 'react';
import { Check } from 'lucide-react';

interface StepIndicatorProps {
  steps: string[];
  currentStep: number;
  className?: string;
}

const StepIndicator: React.FC<StepIndicatorProps> = ({
  steps,
  currentStep,
  className = ''
}) => {
  return (
    <div className={`space-y-4 ${className}`}>
      <div className="flex items-center justify-between">
        <div className="text-sm text-gray-500">
          Step {currentStep} of {steps.length}
        </div>
        <div className="flex space-x-1">
          {steps.map((_, index) => (
            <div
              key={index}
              className={`h-1 w-8 rounded-full ${
                index < currentStep ? 'bg-blue-600' : 'bg-gray-200'
              }`}
            />
          ))}
        </div>
      </div>
      <div className="space-y-4">
        {steps.map((step, index) => (
          <div
            key={index}
            className={`flex items-center ${
              index < currentStep
                ? 'text-blue-600'
                : index === currentStep
                ? 'text-gray-900'
                : 'text-gray-400'
            }`}
          >
            <div
              className={`flex-shrink-0 w-6 h-6 rounded-full border-2 flex items-center justify-center mr-3 ${
                index < currentStep
                  ? 'border-blue-600 bg-blue-600'
                  : index === currentStep
                  ? 'border-gray-900'
                  : 'border-gray-300'
              }`}
            >
              {index < currentStep ? (
                <Check className="h-4 w-4 text-white" />
              ) : (
                <span className="text-sm">{index + 1}</span>
              )}
            </div>
            <span className="text-sm font-medium">{step}</span>
          </div>
        ))}
      </div>
    </div>
  );
};

export default StepIndicator;

/== molecules/StepProgress.tsx
import React from 'react';

interface StepProgressProps {
  currentStep: number;
  totalSteps: number;
}

const StepProgress: React.FC<StepProgressProps> = ({
  currentStep,
  totalSteps
}) => {
  return (
    <div className="mb-6">
      <div className="flex items-center justify-between">
        <div className="text-sm text-gray-500">
          Step {currentStep} of {totalSteps}
        </div>
        <div className="flex space-x-1">
          {Array.from({ length: totalSteps }).map((_, i) => (
            <div
              key={i}
              className={`h-1 w-8 rounded-full ${
                i < currentStep ? 'bg-blue-600' : 'bg-gray-200'
              }`}
            />
          ))}
        </div>
      </div>
    </div>
  );
};

export default StepProgress;

/== molecules/SubmissionChecklist.tsx
import React from 'react';
import { Check, AlertCircle } from 'lucide-react';

interface ChecklistItem {
  id: string;
  label: string;
  required: boolean;
}

interface SubmissionChecklistProps {
  items: ChecklistItem[];
  checkedItems: string[];
  onItemCheck: (id: string) => void;
}

const SubmissionChecklist: React.FC<SubmissionChecklistProps> = ({
  items,
  checkedItems,
  onItemCheck
}) => {
  return (
    <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
      <div className="mb-6">
        <h3 className="text-lg font-semibold text-gray-900 dark:text-white">
          Submission Checklist
        </h3>
        <p className="text-sm text-gray-500 dark:text-gray-400">
          Please verify that your submission meets all requirements
        </p>
      </div>

      <div className="space-y-4">
        {items.map((item) => (
          <div
            key={item.id}
            className="flex items-start p-4 rounded-lg border border-gray-200 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-700/50"
          >
            <div className="flex-shrink-0">
              <input
                type="checkbox"
                checked={checkedItems.includes(item.id)}
                onChange={() => onItemCheck(item.id)}
                className="h-5 w-5 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
              />
            </div>
            <div className="ml-3">
              <label className="text-sm font-medium text-gray-900 dark:text-white">
                {item.label}
              </label>
              {item.required && (
                <span className="ml-2 text-xs text-red-500">*Required</span>
              )}
            </div>
          </div>
        ))}
      </div>

      {items.some(item => item.required && !checkedItems.includes(item.id)) && (
        <div className="mt-4 p-4 bg-yellow-50 dark:bg-yellow-900/50 rounded-lg">
          <div className="flex">
            <AlertCircle className="h-5 w-5 text-yellow-400" />
            <div className="ml-3">
              <p className="text-sm text-yellow-800 dark:text-yellow-200">
                Please complete all required checklist items before submitting
              </p>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default SubmissionChecklist;

/== molecules/SubmissionResource.tsx
import React from 'react';
import { ExternalLink } from 'lucide-react';

interface SubmissionResourceProps {
  title: string;
  description: string;
  link: string;
}

const SubmissionResource: React.FC<SubmissionResourceProps> = ({
  title,
  description,
  link
}) => {
  return (
    <div className="p-4 rounded-lg bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 hover:shadow-lg transition-all duration-200">
      <h3 className="text-lg font-semibold text-gray-900 dark:text-white mb-2">
        {title}
      </h3>
      <p className="text-gray-600 dark:text-gray-300 mb-4 text-sm">
        {description}
      </p>
      <a
        href={link}
        target="_blank"
        rel="noopener noreferrer"
        className="inline-flex items-center text-blue-600 dark:text-blue-400 hover:text-blue-700 text-sm font-medium"
      >
        Learn More
        <ExternalLink className="ml-2 h-4 w-4" />
      </a>
    </div>
  );
};

export default SubmissionResource;

/== molecules/SubmissionStatus.tsx
import React from 'react';
import { Clock, CheckCircle, XCircle } from 'lucide-react';

interface SubmissionStatusProps {
  status: 'reviewing' | 'rejected' | 'approved';
  message?: string;
}

const SubmissionStatus: React.FC<SubmissionStatusProps> = ({ status, message }) => {
  const statusConfig = {
    reviewing: {
      icon: Clock,
      title: 'Under Review',
      color: 'text-blue-500',
      bgColor: 'bg-blue-50 dark:bg-blue-900/20',
      borderColor: 'border-blue-200 dark:border-blue-800'
    },
    rejected: {
      icon: XCircle,
      title: 'Submission Rejected',
      color: 'text-red-500',
      bgColor: 'bg-red-50 dark:bg-red-900/20',
      borderColor: 'border-red-200 dark:border-red-800'
    },
    approved: {
      icon: CheckCircle,
      title: 'Submission Approved',
      color: 'text-green-500',
      bgColor: 'bg-green-50 dark:bg-green-900/20',
      borderColor: 'border-green-200 dark:border-green-800'
    }
  };

  const config = statusConfig[status];
  const Icon = config.icon;

  return (
    <div className={`rounded-lg border ${config.borderColor} ${config.bgColor} p-6`}>
      <div className="flex items-center">
        <div className={`flex-shrink-0 ${config.color}`}>
          <Icon className="h-8 w-8" />
        </div>
        <div className="ml-4">
          <h3 className={`text-lg font-medium ${config.color}`}>
            {config.title}
          </h3>
          {message && (
            <p className="mt-1 text-sm text-gray-600 dark:text-gray-300">
              {message}
            </p>
          )}
        </div>
      </div>
    </div>
  );
};

export default SubmissionStatus;

/== molecules/SubmissionTab.tsx
import React from 'react';

interface SubmissionTabProps {
  label: string;
  isActive: boolean;
  onClick: () => void;
}

const SubmissionTab: React.FC<SubmissionTabProps> = ({ label, isActive, onClick }) => {
  return (
    <button
      onClick={onClick}
      className={`px-6 py-3 text-sm font-medium transition-all duration-200 border-b-2 ${
        isActive
          ? 'text-blue-600 border-blue-600 dark:text-blue-400 dark:border-blue-400'
          : 'text-gray-500 border-transparent hover:text-gray-700 hover:border-gray-300 dark:text-gray-400 dark:hover:text-gray-300'
      }`}
    >
      {label}
    </button>
  );
};

export default SubmissionTab;

/== molecules/SubscriptionCard.tsx
import React, { useState, useEffect } from 'react';

interface Feature {
  name: string;
  included: boolean;
}

interface SubscriptionCardProps {
  id: string;
  type: string;
  title: string;
  subtitle: string;
  price: number;
  pricePeriod: string;
  features: Feature[];
  buttonText: string;
  isPopular?: boolean;
  guarantee?: string;
  onSubscribe: () => void;
  onDelete: () => void;
  onUpdate: (updatedData: any) => void;
}

const SubscriptionCard: React.FC<SubscriptionCardProps> = ({
  id,
  type,
  title,
  subtitle,
  price,
  pricePeriod,
  features,
  buttonText,
  isPopular = false,
  guarantee,
  onSubscribe,
  onDelete,
  onUpdate
}) => {
  const [editing, setEditing] = useState({
    title: false,
    subtitle: false,
    price: false,
    buttonText: false,
    features: null as number | null,
    newFeature: false,
    guarantee: false
  });
  
  const [editedValues, setEditedValues] = useState({
    title,
    subtitle,
    price,
    buttonText,
    features: [...features],
    newFeature: '',
    guarantee: guarantee || ''
  });
  
  // Update edited values when props change
  useEffect(() => {
    setEditedValues(prev => ({
      ...prev,
      title,
      subtitle,
      price,
      buttonText,
      features: [...features],
      guarantee: guarantee || ''
    }));
  }, [title, subtitle, price, buttonText, features, guarantee]);
  
  // Simplified handleEdit function
  const handleEdit = (field: string, value: any = null) => {
    // Create a new editing state object where everything is initially false/null
    const newEditing = {
      title: false,
      subtitle: false,
      price: false,
      buttonText: false,
      features: null as number | null,
      newFeature: false,
      guarantee: false
    };
    
    // Set only the clicked field to true/value
    if (field === 'features') {
      newEditing.features = value;
    } else {
      // @ts-ignore - we know field is a valid key
      newEditing[field] = true;
    }
    
    setEditing(newEditing);
  };
  
  const handleSave = (field: string, index: number | null = null) => {
    // Create a copy of the current edited values
    const updatedValues = { ...editedValues };
    
    // Handle adding a new feature
    if (field === 'newFeature' && editedValues.newFeature.trim() !== '') {
      const newFeature = {
        name: editedValues.newFeature.trim(),
        included: true
      };
      
      // Update the features array with the new feature
      updatedValues.features = [...updatedValues.features, newFeature];
      
      // Reset the new feature input
      updatedValues.newFeature = '';
      
      // Update local state with new feature added
      setEditedValues(updatedValues);
      
      // Send the updated data to parent component
      onUpdate(updatedValues);
    }
    
    // Close the editing state
    const newEditing = { ...editing };
    if (field === 'features') {
      newEditing.features = null;
    } else {
      // @ts-ignore - we know field is a valid key
      newEditing[field] = false;
    }
    
    setEditing(newEditing);
    
    // Send the updated data to parent for other fields
    if (field !== 'newFeature') {
      onUpdate(updatedValues);
    }
  };
  
  const handleInputChange = (field: string, value: any, index: number | null = null) => {
    const updatedValues = { ...editedValues };
    
    if (field === 'features' && index !== null) {
      updatedValues.features[index] = {
        ...updatedValues.features[index],
        name: value
      };
    } else {
      // @ts-ignore - we know field is a valid key
      updatedValues[field] = value;
    }
    
    setEditedValues(updatedValues);
  };
  
  const handleAddFeature = () => {
    handleEdit('newFeature');
  };
  
  const handleDeleteFeature = (index: number) => {
    const updatedFeatures = [...editedValues.features];
    updatedFeatures.splice(index, 1);
    
    const updatedValues = {
      ...editedValues,
      features: updatedFeatures
    };
    
    setEditedValues(updatedValues);
    onUpdate(updatedValues);
  };
  
  return (
    <div className={`bg-white dark:bg-gray-800 rounded-lg overflow-hidden border ${isPopular ? 'border-green-500 border-1' : 'border-gray-100'} relative`}>
      {/* Delete button */}
      <button 
        onClick={onDelete}
        className="absolute top-2 right-2 text-gray-400 hover:text-red-500 z-10"
        aria-label="Delete subscription"
      >
        <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
        </svg>
      </button>
      
      {isPopular && (
        <div className="absolute top-0 right-12 bg-green-500 text-white dark:text-gray-800 px-4 py-1 text-sm font-medium rounded-b-lg">
          Most Popular
        </div>
      )}
      
      <div className="p-6">
        {/* Title */}
        <div className="text-center mb-4">
          {editing.title ? (
            <div className="mb-2">
              <input
                type="text"
                value={editedValues.title}
                onChange={(e) => handleInputChange('title', e.target.value)}
                className="border border-gray-100 rounded px-2 py-1 text-xl font-bold w-full text-center"
                autoFocus
                onBlur={() => handleSave('title')}
                onKeyPress={(e) => e.key === 'Enter' && handleSave('title')}
              />
            </div>
          ) : (
            <h3 
              className="text-xl font-bold cursor-pointer hover:text-blue-600 transition-colors" 
              onClick={() => handleEdit('title')}
            >
              {editedValues.title}
            </h3>
          )}
          
          {/* Subtitle - now editable */}
          {editing.subtitle ? (
            <div className="mb-2">
              <input
                type="text"
                value={editedValues.subtitle}
                onChange={(e) => handleInputChange('subtitle', e.target.value)}
                className="border border-gray-100 rounded px-2 py-1 text-gray-600 w-full text-center"
                autoFocus
                onBlur={() => handleSave('subtitle')}
                onKeyPress={(e) => e.key === 'Enter' && handleSave('subtitle')}
              />
            </div>
          ) : (
            <p 
              className="text-gray-600 dark:text-gray-400 cursor-pointer hover:text-blue-600 transition-colors"
              onClick={() => handleEdit('subtitle')}
            >
              {editedValues.subtitle}
            </p>
          )}
        </div>
        
        {/* Price */}
        <div className="text-center mb-6">
          {editing.price ? (
            <div className="mb-2">
              <input
                type="number"
                value={editedValues.price}
                onChange={(e) => handleInputChange('price', parseInt(e.target.value) || 0)}
                className="border border-gray-300 rounded px-2 py-1 text-3xl font-bold w-full text-center"
                autoFocus
                onBlur={() => handleSave('price')}
                onKeyPress={(e) => e.key === 'Enter' && handleSave('price')}
              />
            </div>
          ) : (
            <div 
              className="text-3xl font-bold cursor-pointer hover:text-blue-600 transition-colors" 
              onClick={() => handleEdit('price')}
            >
              <span className="text-black dark:text-white">Rp {parseInt(String(price)).toLocaleString('id-ID')}</span>
              <span className="text-sm text-gray-500 font-normal ml-1">/ {pricePeriod}</span>
            </div>
          )}
        </div>
        
        {/* Subscribe Button - now editable */}
        {editing.buttonText ? (
          <div className="mb-6">
            <input
              type="text"
              value={editedValues.buttonText}
              onChange={(e) => handleInputChange('buttonText', e.target.value)}
              className="border border-gray-300 rounded px-2 py-1 w-full text-center"
              autoFocus
              onBlur={() => handleSave('buttonText')}
              onKeyPress={(e) => e.key === 'Enter' && handleSave('buttonText')}
            />
          </div>
        ) : (
          <button
            onClick={onSubscribe}
            className={`w-full py-3 px-4 rounded-lg font-medium mb-6 ${
              type === 'free' 
                ? 'bg-gray-100 text-gray-800 hover:bg-gray-200' 
                : isPopular
                  ? 'bg-green-500 text-white dark:text-white hover:bg-green-600'
                  : 'bg-blue-500 text-white dark:text-white hover:bg-blue-600'
            }`}
            onDoubleClick={(e) => {
              e.preventDefault();
              e.stopPropagation();
              handleEdit('buttonText');
            }}
          >
            {editedValues.buttonText}
          </button>
        )}
        
        {/* Features */}
        <div className="space-y-4">
          {editedValues.features.map((feature, index) => (
            <div key={index} className="flex items-start group">
              {/* Checkmark icon */}
              <div className="text-green-500 mt-0.5 mr-3 flex-shrink-0">
                <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 13l4 4L19 7" />
                </svg>
              </div>
              
              {/* Feature text/input */}
              <div className="flex-grow">
                {editing.features === index ? (
                  <input
                    type="text"
                    value={editedValues.features[index].name}
                    onChange={(e) => handleInputChange('features', e.target.value, index)}
                    className="border border-gray-300 rounded px-2 py-1 w-full"
                    autoFocus
                    onBlur={() => handleSave('features')}
                    onKeyPress={(e) => e.key === 'Enter' && handleSave('features')}
                  />
                ) : (
                  <span 
                    className="cursor-pointer hover:text-blue-600 transition-colors"
                    onClick={() => handleEdit('features', index)}
                  >
                    {feature.name}
                  </span>
                )}
              </div>
              
              {/* Delete feature button */}
              <button 
                onClick={() => handleDeleteFeature(index)}
                className="hidden group-hover:block text-gray-400 hover:text-red-500 ml-2"
                aria-label="Delete feature"
              >
                <svg className="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M6 18L18 6M6 6l12 12" />
                </svg>
              </button>
            </div>
          ))}
          
          {/* Add new feature section */}
          {editing.newFeature ? (
            <div className="flex items-start border-t border-gray-100 pt-4 mt-4">
              <div className="text-green-500 mt-0.5 mr-3 flex-shrink-0">
                <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 13l4 4L19 7" />
                </svg>
              </div>
              <input
                type="text"
                value={editedValues.newFeature}
                onChange={(e) => handleInputChange('newFeature', e.target.value)}
                className="border border-gray-300 rounded px-2 py-1 w-full"
                placeholder="Add new feature..."
                autoFocus
                onBlur={() => handleSave('newFeature')}
                onKeyPress={(e) => e.key === 'Enter' && handleSave('newFeature')}
              />
            </div>
          ) : (
            <div className="border-t border-gray-100 pt-4 mt-4">
              <button
                onClick={handleAddFeature}
                className="flex items-center text-sm text-blue-500 hover:text-blue-700"
              >
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
                </svg>
                Add feature
              </button>
            </div>
          )}
        </div>
        
        {/* Guarantee - now editable */}
        {editing.guarantee ? (
          <div className="mt-6">
            <input
              type="text"
              value={editedValues.guarantee}
              onChange={(e) => handleInputChange('guarantee', e.target.value)}
              className="border border-gray-300 rounded px-2 py-1 w-full text-center text-sm text-gray-500"
              autoFocus
              onBlur={() => handleSave('guarantee')}
              onKeyPress={(e) => e.key === 'Enter' && handleSave('guarantee')}
              placeholder="Add guarantee text..."
            />
          </div>
        ) : (
          editedValues.guarantee ? (
            <div 
              className="mt-6 text-center text-sm text-gray-500 cursor-pointer hover:text-blue-600 transition-colors"
              onClick={() => handleEdit('guarantee')}
            >
              {editedValues.guarantee}
            </div>
          ) : (
            <div className="mt-6 text-center">
              <button
                onClick={() => handleEdit('guarantee')}
                className="text-sm text-blue-500 hover:text-blue-700"
              >
                Add guarantee
              </button>
            </div>
          )
        )}
      </div>
    </div>
  );
};

export default SubscriptionCard;

/== molecules/TabGroup.tsx
import React from 'react';

interface Tab {
  id: string;
  label: string;
}

interface TabGroupProps {
  tabs: Tab[];
  activeTab: string;
  onChange: (tabId: string) => void;
  className?: string;
}

const TabGroup: React.FC<TabGroupProps> = ({
  tabs,
  activeTab,
  onChange,
  className = ''
}) => {
  return (
    <div className={`border-b border-gray-200 ${className}`}>
      <nav className="-mb-px flex space-x-8">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => onChange(tab.id)}
            className={`
              whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm
              ${
                activeTab === tab.id
                  ? 'border-blue-500 text-blue-600'
                  : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
              }
            `}
          >
            {tab.label}
          </button>
        ))}
      </nav>
    </div>
  );
};

export default TabGroup;

/== molecules/TableOfContents.tsx
import React from 'react';
import { ChevronRight } from 'lucide-react';
import Card from '../atoms/Card';
import Text from '../atoms/Text';
import IconText from './IconText';

interface TableOfContentsProps {
  topics: {
    title: string;
    path: string;
  }[];
}

const TableOfContents: React.FC<TableOfContentsProps> = ({ topics }) => {
  return (
    <Card className="p-6 sticky top-20">
      <Text variant="body" className="font-bold text-gray-900 mb-4">Table of Contents</Text>
      <ul className="space-y-2">
        {topics.map((topic) => (
          <li key={topic.path}>
            <IconText
              icon={ChevronRight}
              text={topic.title}
              className="text-gray-700 hover:text-blue-600 cursor-pointer"
            />
          </li>
        ))}
      </ul>
    </Card>
  );
};

export default TableOfContents;

/== molecules/TutorialCard.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import Card from '../atoms/Card';
import Text from '../atoms/Text';
import Button from '../atoms/Button';
import { ChevronRight } from 'lucide-react';

interface TutorialCardProps {
  title: string;
  description: string;
  path: string;
  icon: string;
  color: string;
}

const TutorialCard: React.FC<TutorialCardProps> = ({
  title,
  description,
  path,
  icon,
  color
}) => {
  return (
    <Link to={path}>
      <Card className="p-6 hover:shadow-lg transition duration-300">
        <div className={`text-3xl mb-3 ${color} h-16 w-16 flex items-center justify-center rounded-full`}>
          {icon}
        </div>
        <Text variant="body" className="font-medium text-gray-900 mb-2">{title}</Text>
        <Text variant="caption" color="muted">{description}</Text>
        <div className="mt-4 flex items-center text-blue-600">
          <span className="text-sm font-medium">Learn More</span>
          <ChevronRight className="h-4 w-4 ml-1" />
        </div>
      </Card>
    </Link>
  );
};

export default TutorialCard;

/== molecules/UserAvatar.tsx
import React from 'react';
import Avatar from '../atoms/Avatar';
import Text from '../atoms/Text';

interface UserAvatarProps {
  name: string;
  image?: string;
  subtitle?: string;
  size?: 'sm' | 'md' | 'lg';
}

const UserAvatar: React.FC<UserAvatarProps> = ({
  name,
  image,
  subtitle,
  size = 'md'
}) => {
  return (
    <div className="flex items-center">
      <Avatar src={image} alt={name} size={size} />
      <div className="ml-3">
        <Text variant="body" className="font-medium">{name}</Text>
        {subtitle && (
          <Text variant="caption" color="muted">{subtitle}</Text>
        )}
      </div>
    </div>
  );
};

export default UserAvatar;

/== organisms/AffiliateAnalytics.tsx
import React from "react";
import Card from "../atoms/Card";
import Heading from "../atoms/Heading";
import Text from "../atoms/Text";
import Divider from "../atoms/Divider";
import Progress from "../atoms/Progress";
import { AffiliateAnalytics as AnalyticsType } from "../../store/affiliateStore";

interface AffiliateAnalyticsProps {
  data: AnalyticsType;
}

const AffiliateAnalytics: React.FC<AffiliateAnalyticsProps> = ({ data }) => {
  // Calculate max values for scaling bars
  const maxEarnings = Math.max(...data.monthlyStats.map(item => item.earnings));
  const maxRegistrations = Math.max(...data.monthlyStats.map(item => item.registrations));
  const maxConversions = Math.max(...data.monthlyStats.map(item => item.conversions));

  return (
    <div className="space-y-6">
      <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <Card className="p-4 dark:bg-gray-800">
          <Text className="text-sm text-gray-500 dark:text-gray-400">Total Pendapatan</Text>
          <Heading level={3} className="mt-1 text-gray-900 dark:text-white">
            Rp {data.totalEarnings.toLocaleString()}
          </Heading>
        </Card>
        <Card className="p-4 dark:bg-gray-800">
          <Text className="text-sm text-gray-500 dark:text-gray-400">Total Pendaftaran</Text>
          <Heading level={3} className="mt-1 text-gray-900 dark:text-white">
            {data.totalRegistrations}
          </Heading>
        </Card>
        <Card className="p-4 dark:bg-gray-800">
          <Text className="text-sm text-gray-500 dark:text-gray-400">Total Konversi</Text>
          <Heading level={3} className="mt-1 text-gray-900 dark:text-white">
            {data.totalConversions}
          </Heading>
        </Card>
        <Card className="p-4 dark:bg-gray-800">
          <Text className="text-sm text-gray-500 dark:text-gray-400">Tingkat Konversi</Text>
          <Heading level={3} className="mt-1 text-gray-900 dark:text-white">
            {data.conversionRate.toFixed(1)}%
          </Heading>
        </Card>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <Card className="p-4 dark:bg-gray-800">
          <Heading level={4} className="mb-4 text-gray-900 dark:text-white">
            Trend Pendapatan Bulanan
          </Heading>
          <div className="space-y-4">
            {data.monthlyStats.map((month, index) => (
              <div key={index} className="space-y-1">
                <div className="flex justify-between text-sm">
                  <Text className="text-gray-700 dark:text-gray-300">{month.name}</Text>
                  <Text className="text-gray-900 dark:text-white font-medium">
                    Rp {month.earnings.toLocaleString()}
                  </Text>
                </div>
                <Progress 
                  value={(month.earnings / maxEarnings) * 100} 
                  color="blue" 
                  className="h-2" 
                />
              </div>
            ))}
          </div>
        </Card>

        <Card className="p-4 dark:bg-gray-800">
          <Heading level={4} className="mb-4 text-gray-900 dark:text-white">
            Aktivitas Referral
          </Heading>
          <div className="space-y-6">
            <div>
              <Text className="font-medium mb-2 text-gray-900 dark:text-white">Pendaftaran</Text>
              {data.monthlyStats.map((month, index) => (
                <div key={`reg-${index}`} className="mb-2">
                  <div className="flex justify-between text-sm">
                    <Text className="text-gray-700 dark:text-gray-300">{month.name}</Text>
                    <Text className="text-gray-900 dark:text-white">{month.registrations}</Text>
                  </div>
                  <Progress 
                    value={(month.registrations / maxRegistrations) * 100} 
                    color="blue" 
                    className="h-2" 
                  />
                </div>
              ))}
            </div>

            <Divider className="dark:border-gray-700" />

            <div>
              <Text className="font-medium mb-2 text-gray-900 dark:text-white">Konversi</Text>
              {data.monthlyStats.map((month, index) => (
                <div key={`conv-${index}`} className="mb-2">
                  <div className="flex justify-between text-sm">
                    <Text className="text-gray-700 dark:text-gray-300">{month.name}</Text>
                    <Text className="text-gray-900 dark:text-white">{month.conversions}</Text>
                  </div>
                  <Progress 
                    value={(month.conversions / maxConversions) * 100} 
                    color="green" 
                    className="h-2" 
                  />
                </div>
              ))}
            </div>
          </div>
        </Card>
      </div>

      <Card className="p-4 dark:bg-gray-800">
        <Heading level={4} className="mb-4 text-gray-900 dark:text-white">Top Afiliator</Heading>
        <div className="overflow-x-auto">
          <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
            <thead className="bg-gray-50 dark:bg-gray-800">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Afiliator
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Konversi
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Tingkat Konversi
                </th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                  Pendapatan
                </th>
              </tr>
            </thead>
            <tbody className="bg-white dark:bg-gray-900 divide-y divide-gray-200 dark:divide-gray-700">
              {data.topAffiliates.map((affiliate, index) => (
                <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-800">
                  <td className="px-6 py-4 whitespace-nowrap">
                    <Text className="font-medium text-gray-900 dark:text-white">{affiliate.name}</Text>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <Text className="text-gray-700 dark:text-gray-300">{affiliate.conversions}</Text>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <Text className="text-gray-700 dark:text-gray-300">
                      {affiliate.conversionRate.toFixed(1)}%
                    </Text>
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <Text className="font-medium text-gray-900 dark:text-white">
                      Rp {affiliate.earnings.toLocaleString()}
                    </Text>
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

export default AffiliateAnalytics;

/== organisms/AffiliateApplicationForm.tsx
import React, { useState } from "react";
import Card from "../atoms/Card";
import Heading from "../atoms/Heading";
import Text from "../atoms/Text";
import Input from "../atoms/Input";
import TextArea from "../atoms/TextArea";
import Button from "../atoms/Button";
import Select from "../atoms/Select";
import StepIndicator from "../molecules/StepIndicator";

interface AffiliateApplicationFormProps {
  onSubmit: (data: any) => void;
}

const AffiliateApplicationForm: React.FC<AffiliateApplicationFormProps> = ({
  onSubmit,
}) => {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    phone: "",
    website: "",
    socialMedia: {
      instagram: "",
      facebook: "",
      twitter: "",
      linkedin: "",
      youtube: "",
    },
    occupation: "",
    experience: "",
    audience: "",
    reason: "",
    marketingPlan: "",
    paymentMethod: "bank",
    bankAccount: {
      name: "",
      number: "",
      bank: "",
    },
    eWallet: {
      provider: "",
      number: "",
    },
    termsAccepted: false,
  });

  const handleChange = (
    e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>
  ) => {
    const { name, value, type } = e.target;

    if (type === "checkbox") {
      const checkbox = e.target as HTMLInputElement;
      setFormData({ ...formData, [name]: checkbox.checked });
    } else if (name.includes(".")) {
      const [parent, child] = name.split(".");
      setFormData({
        ...formData,
        [parent]: {
          ...formData[parent as keyof typeof formData],
          [child]: value,
        },
      });
    } else {
      setFormData({ ...formData, [name]: value });
    }
  };

  const nextStep = () => {
    setStep(step + 1);
    window.scrollTo(0, 0);
  };

  const prevStep = () => {
    setStep(step - 1);
    window.scrollTo(0, 0);
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit(formData);
  };

  return (
    <Card className="p-6 max-w-4xl mx-auto">
      <div className="mb-6">
        <Heading level={2}>Aplikasi Program Afiliasi</Heading>
        <Text className="text-gray-500 mt-1">
          Isi formulir di bawah untuk bergabung dengan program afiliasi kami
        </Text>
      </div>

      <div className="mb-8">
        <StepIndicator
          steps={["Informasi Pribadi", "Pengalaman & Rencana", "Detail Pembayaran"]}
          currentStep={step}
        />
      </div>

      <form onSubmit={handleSubmit}>
        {step === 1 && (
          <div className="space-y-4">
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Input
                label="Nama Lengkap"
                name="name"
                value={formData.name}
                onChange={handleChange}
                required
              />
              <Input
                label="Alamat Email"
                name="email"
                type="email"
                value={formData.email}
                onChange={handleChange}
                required
              />
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Input
                label="Nomor Telepon"
                name="phone"
                value={formData.phone}
                onChange={handleChange}
                required
              />
              <Input
                label="Website (opsional)"
                name="website"
                value={formData.website}
                onChange={handleChange}
              />
            </div>

            <Heading level={4} className="mt-6">
              Sosial Media
            </Heading>
            <Text className="text-sm text-gray-500 mb-3">
              Tambahkan akun sosial media yang akan Anda gunakan untuk promosi
            </Text>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Input
                label="Instagram"
                name="socialMedia.instagram"
                value={formData.socialMedia.instagram}
                onChange={handleChange}
              />
              <Input
                label="Facebook"
                name="socialMedia.facebook"
                value={formData.socialMedia.facebook}
                onChange={handleChange}
              />
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Input
                label="Twitter"
                name="socialMedia.twitter"
                value={formData.socialMedia.twitter}
                onChange={handleChange}
              />
              <Input
                label="LinkedIn"
                name="socialMedia.linkedin"
                value={formData.socialMedia.linkedin}
                onChange={handleChange}
              />
            </div>

            <Input
              label="YouTube"
              name="socialMedia.youtube"
              value={formData.socialMedia.youtube}
              onChange={handleChange}
            />

            <div className="flex justify-end mt-6">
              <Button onClick={nextStep}>Lanjutkan</Button>
            </div>
          </div>
        )}

        {step === 2 && (
          <div className="space-y-4">
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Input
                label="Pekerjaan/Profesi"
                name="occupation"
                value={formData.occupation}
                onChange={handleChange}
                required
              />
              <Select
                label="Pengalaman di Bidang Teknologi/Pendidikan"
                name="experience"
                value={formData.experience}
                onChange={handleChange}
                options={[
                  { value: "", label: "Pilih..." },
                  { value: "beginner", label: "Pemula (< 1 tahun)" },
                  { value: "intermediate", label: "Menengah (1-3 tahun)" },
                  { value: "advanced", label: "Mahir (3-5 tahun)" },
                  { value: "expert", label: "Ahli (5+ tahun)" },
                ]}
                required
              />
            </div>

            <Select
              label="Target Audiens Anda"
              name="audience"
              value={formData.audience}
              onChange={handleChange}
              options={[
                { value: "", label: "Pilih..." },
                { value: "students", label: "Pelajar/Mahasiswa" },
                { value: "professionals", label: "Profesional" },
                { value: "educators", label: "Pendidik" },
                { value: "companies", label: "Perusahaan" },
                { value: "mixed", label: "Campuran" },
              ]}
              required
            />

            <TextArea
              label="Mengapa Anda ingin bergabung dengan program afiliasi kami?"
              name="reason"
              value={formData.reason}
              onChange={handleChange}
              rows={3}
              required
            />

            <TextArea
              label="Bagaimana rencana Anda untuk mempromosikan kursus kami?"
              name="marketingPlan"
              value={formData.marketingPlan}
              onChange={handleChange}
              rows={4}
              required
              placeholder="Jelaskan strategi dan kanal yang akan Anda gunakan untuk mempromosikan kursus kami."
            />

            <div className="flex justify-between mt-6">
              <Button variant="outline" onClick={prevStep}>
                Kembali
              </Button>
              <Button onClick={nextStep}>Lanjutkan</Button>
            </div>
          </div>
        )}

        {step === 3 && (
          <div className="space-y-4">
            <div>
              <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
                Metode Pembayaran
              </label>
              <div className="flex space-x-4">
                <div className="flex items-center">
                  <input
                    type="radio"
                    id="bank"
                    name="paymentMethod"
                    value="bank"
                    checked={formData.paymentMethod === "bank"}
                    onChange={handleChange}
                    className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300"
                  />
                  <label htmlFor="bank" className="ml-2 text-sm text-gray-700 dark:text-gray-300">
                    Rekening Bank
                  </label>
                </div>
                <div className="flex items-center">
                  <input
                    type="radio"
                    id="ewallet"
                    name="paymentMethod"
                    value="ewallet"
                    checked={formData.paymentMethod === "ewallet"}
                    onChange={handleChange}
                    className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300"
                  />
                  <label htmlFor="ewallet" className="ml-2 text-sm text-gray-700 dark:text-gray-300">
                    E-Wallet
                  </label>
                </div>
              </div>
            </div>

            {formData.paymentMethod === "bank" ? (
              <div className="space-y-4">
                <Input
                  label="Nama Pemilik Rekening"
                  name="bankAccount.name"
                  value={formData.bankAccount.name}
                  onChange={handleChange}
                  required
                />
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  <Input
                    label="Nama Bank"
                    name="bankAccount.bank"
                    value={formData.bankAccount.bank}
                    onChange={handleChange}
                    required
                  />
                  <Input
                    label="Nomor Rekening"
                    name="bankAccount.number"
                    value={formData.bankAccount.number}
                    onChange={handleChange}
                    required
                  />
                </div>
              </div>
            ) : (
              <div className="space-y-4">
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  <Input
                    label="Provider E-Wallet"
                    name="eWallet.provider"
                    value={formData.eWallet.provider}
                    onChange={handleChange}
                    placeholder="OVO/GoPay/DANA/LinkAja"
                    required
                  />
                  <Input
                    label="Nomor E-Wallet"
                    name="eWallet.number"
                    value={formData.eWallet.number}
                    onChange={handleChange}
                    required
                  />
                </div>
              </div>
            )}

            <div className="mt-6">
              <div className="flex items-start">
                <div className="flex items-center h-5">
                  <input
                    id="termsAccepted"
                    name="termsAccepted"
                    type="checkbox"
                    checked={formData.termsAccepted}
                    onChange={handleChange}
                    className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
                    required
                  />
                </div>
                <div className="ml-3 text-sm">
                  <label htmlFor="termsAccepted" className="text-gray-700 dark:text-gray-300">
                    Saya menyetujui <a href="#" className="text-blue-600 hover:underline">syarat dan ketentuan</a> program afiliasi
                  </label>
                </div>
              </div>
            </div>

            <div className="flex justify-between mt-6">
              <Button variant="outline" onClick={prevStep}>
                Kembali
              </Button>
              <Button type="submit" disabled={!formData.termsAccepted}>
                Kirim Aplikasi
              </Button>
            </div>
          </div>
        )}
      </form>
    </Card>
  );
};

export default AffiliateApplicationForm;

/== organisms/AffiliateDashboard.tsx
import React from "react";
import Card from "../atoms/Card";
import Heading from "../atoms/Heading";
import Text from "../atoms/Text";
import Button from "../atoms/Button";
import Input from "../atoms/Input";
import Badge from "../atoms/Badge";
import IconButton from "../atoms/IconButton";
import Progress from "../atoms/Progress";
import TabGroup from "../molecules/TabGroup";

interface AffiliateDashboardProps {
  userData: {
    name: string;
    referralCode: string;
    totalEarnings: number;
    availableEarnings: number;
    pendingEarnings: number;
    referralCount: number;
    conversionCount: number;
    conversionRate: number;
    level: "bronze" | "silver" | "gold" | "platinum";
    progress: number;
    nextLevelThreshold: number;
  };
  referrals: Array<{
    id: string;
    name: string;
    date: string;
    status: "pending" | "converted" | "expired";
    commission: number;
  }>;
  payouts: Array<{
    id: string;
    amount: number;
    status: "pending" | "processed" | "completed";
    date: string;
    method: string;
  }>;
}

const AffiliateDashboard: React.FC<AffiliateDashboardProps> = ({
  userData,
  referrals,
  payouts,
}) => {
  const [activeTab, setActiveTab] = React.useState("dashboard");
  const [showCopyToast, setShowCopyToast] = React.useState(false);

  const copyReferralLink = () => {
    navigator.clipboard.writeText(
      `https://example.com/refer?code=${userData.referralCode}`
    );
    setShowCopyToast(true);
    setTimeout(() => setShowCopyToast(false), 3000);
  };

  const getLevelColor = (level: string) => {
    switch (level) {
      case "bronze":
        return "bg-amber-700";
      case "silver":
        return "bg-gray-400";
      case "gold":
        return "bg-yellow-500";
      case "platinum":
        return "bg-blue-300";
      default:
        return "bg-gray-300";
    }
  };

  const getStatusColor = (status: string) => {
    switch (status) {
      case "converted":
      case "completed":
        return "green";
      case "pending":
        return "yellow";
      case "expired":
      case "cancelled":
        return "red";
      default:
        return "gray";
    }
  };

  const tabs = [
    { id: "dashboard", label: "Dashboard" },
    { id: "referrals", label: "Referral Saya" },
    { id: "payouts", label: "Pencairan Dana" },
    { id: "materials", label: "Materi Promosi" },
  ];

  return (
    <div className="space-y-6">
      {showCopyToast && (
        <div className="fixed top-4 right-4 bg-green-500 text-white px-4 py-2 rounded-md shadow-lg z-50 animate-fade-in-out">
          Link referral berhasil disalin!
        </div>
      )}

      <TabGroup tabs={tabs} activeTab={activeTab} onChange={setActiveTab} />

      {activeTab === "dashboard" && (
        <>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-6">
            <Card className="p-4">
              <Text className="text-sm text-gray-500 dark:text-gray-400">
                Total Penghasilan
              </Text>
              <Heading level={3} className="mt-1">
                Rp {userData.totalEarnings.toLocaleString()}
              </Heading>
              <div className="mt-2 flex items-center space-x-4">
                <div>
                  <Text className="text-xs text-gray-500">Tersedia</Text>
                  <Text className="font-medium">
                    Rp {userData.availableEarnings.toLocaleString()}
                  </Text>
                </div>
                <div>
                  <Text className="text-xs text-gray-500">Tertunda</Text>
                  <Text className="font-medium">
                    Rp {userData.pendingEarnings.toLocaleString()}
                  </Text>
                </div>
              </div>
            </Card>

            <Card className="p-4">
              <Text className="text-sm text-gray-500 dark:text-gray-400">
                Total Referral
              </Text>
              <Heading level={3} className="mt-1">
                {userData.referralCount}
              </Heading>
              <div className="mt-2 flex items-center space-x-4">
                <div>
                  <Text className="text-xs text-gray-500">Konversi</Text>
                  <Text className="font-medium">{userData.conversionCount}</Text>
                </div>
                <div>
                  <Text className="text-xs text-gray-500">Tingkat Konversi</Text>
                  <Text className="font-medium">{userData.conversionRate}%</Text>
                </div>
              </div>
            </Card>

            <Card className="p-4">
              <div className="flex justify-between mb-2">
                <Text className="text-sm text-gray-500 dark:text-gray-400">
                  Level Afiliasi
                </Text>
                <Badge
                  color="blue"
                  className={`${getLevelColor(userData.level)} text-white`}
                >
                  {userData.level.charAt(0).toUpperCase() + userData.level.slice(1)}
                </Badge>
              </div>
              <div className="mt-2">
                <div className="flex justify-between mb-1">
                  <Text className="text-xs">
                    {userData.progress} / {userData.nextLevelThreshold} Konversi
                  </Text>
                  <Text className="text-xs">
                    {Math.round((userData.progress / userData.nextLevelThreshold) * 100)}%
                  </Text>
                </div>
                <Progress
                  value={(userData.progress / userData.nextLevelThreshold) * 100}
                  color="blue"
                  className="h-2"
                />
                <Text className="text-xs text-gray-500 mt-2">
                  {userData.nextLevelThreshold - userData.progress} konversi lagi untuk naik level
                </Text>
              </div>
            </Card>
          </div>

          <Card className="p-6">
            <div className="flex flex-col md:flex-row justify-between items-start md:items-center mb-6">
              <div>
                <Heading level={3}>Link Referral Anda</Heading>
                <Text className="text-gray-500 mt-1">
                  Bagikan link ini dan dapatkan komisi untuk setiap pendaftaran
                </Text>
              </div>
              <Button
                variant="primary"
                className="mt-4 md:mt-0"
                onClick={copyReferralLink}
              >
                Salin Link
              </Button>
            </div>

            <div className="bg-gray-50 dark:bg-gray-800 p-4 rounded-md">
              <div className="flex flex-col md:flex-row items-start md:items-center justify-between">
                <div className="flex-grow">
                  <div className="flex items-center">
                    <Text className="font-medium">Kode Referral:</Text>
                    <Badge color="blue" className="ml-2">
                      {userData.referralCode}
                    </Badge>
                  </div>
                  <Input
                    className="mt-2"
                    value={`https://example.com/refer?code=${userData.referralCode}`}
                    readOnly
                    onClick={() => copyReferralLink()}
                  />
                </div>
              </div>
            </div>
          </Card>

          <Card className="p-6">
            <Heading level={3} className="mb-4">
              Aktivitas Terbaru
            </Heading>
            <div className="overflow-x-auto">
              <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                <thead className="bg-gray-50 dark:bg-gray-800">
                  <tr>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Nama
                    </th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Tanggal
                    </th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Status
                    </th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Komisi
                    </th>
                  </tr>
                </thead>
                <tbody className="bg-white dark:bg-gray-900 divide-y divide-gray-200 dark:divide-gray-700">
                  {referrals.slice(0, 5).map((referral) => (
                    <tr key={referral.id}>
                      <td className="px-6 py-4 whitespace-nowrap">
                        <Text className="font-medium">{referral.name}</Text>
                      </td>
                      <td className="px-6 py-4 whitespace-nowrap">
                        <Text>{referral.date}</Text>
                      </td>
                      <td className="px-6 py-4 whitespace-nowrap">
                        <Badge color={getStatusColor(referral.status)}>
                          {referral.status === "converted"
                            ? "Berhasil"
                            : referral.status === "pending"
                            ? "Tertunda"
                            : "Kedaluwarsa"}
                        </Badge>
                      </td>
                      <td className="px-6 py-4 whitespace-nowrap">
                        <Text className="font-medium">
                          Rp {referral.commission.toLocaleString()}
                        </Text>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
            {referrals.length > 5 && (
              <div className="mt-4 text-center">
                <Button variant="link" onClick={() => setActiveTab("referrals")}>
                  Lihat Semua Referral
                </Button>
              </div>
            )}
          </Card>
        </>
      )}

      {activeTab === "referrals" && (
        <Card className="p-6">
          <Heading level={3} className="mb-4">
            Daftar Referral
          </Heading>
          <div className="overflow-x-auto">
            <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
              <thead className="bg-gray-50 dark:bg-gray-800">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Nama
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Tanggal
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Status
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Komisi
                  </th>
                </tr>
              </thead>
              <tbody className="bg-white dark:bg-gray-900 divide-y divide-gray-200 dark:divide-gray-700">
                {referrals.map((referral) => (
                  <tr key={referral.id}>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text className="font-medium">{referral.name}</Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text>{referral.date}</Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Badge color={getStatusColor(referral.status)}>
                        {referral.status === "converted"
                          ? "Berhasil"
                          : referral.status === "pending"
                          ? "Tertunda"
                          : "Kedaluwarsa"}
                      </Badge>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text className="font-medium">
                        Rp {referral.commission.toLocaleString()}
                      </Text>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </Card>
      )}

      {activeTab === "payouts" && (
        <Card className="p-6">
          <div className="flex justify-between items-center mb-6">
            <div>
              <Heading level={3}>Pencairan Dana</Heading>
              <Text className="text-gray-500 mt-1">
                Kelola pencairan komisi afiliasi Anda
              </Text>
            </div>
            <Button>Ajukan Pencairan</Button>
          </div>
          
          <div className="overflow-x-auto">
            <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
              <thead className="bg-gray-50 dark:bg-gray-800">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    ID Transaksi
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Jumlah
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Tanggal
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Metode
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                    Status
                  </th>
                </tr>
              </thead>
              <tbody className="bg-white dark:bg-gray-900 divide-y divide-gray-200 dark:divide-gray-700">
                {payouts.map((payout) => (
                  <tr key={payout.id}>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text className="font-mono">{payout.id}</Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text className="font-medium">
                        Rp {payout.amount.toLocaleString()}
                      </Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text>{payout.date}</Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Text>{payout.method}</Text>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <Badge color={getStatusColor(payout.status)}>
                        {payout.status === "completed"
                          ? "Selesai"
                          : payout.status === "processed"
                          ? "Diproses"
                          : "Tertunda"}
                      </Badge>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </Card>
      )}

      {activeTab === "materials" && (
        <div className="space-y-6">
          <Card className="p-6">
            <Heading level={3} className="mb-4">
              Banner Promosi
            </Heading>
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
              <div className="border border-gray-200 dark:border-gray-700 rounded-md overflow-hidden">
                <img
                  src="https://via.placeholder.com/800x400?text=Promo+Banner+1"
                  alt="Promo Banner 1"
                  className="w-full object-cover"
                />
                <div className="p-3">
                  <Text className="font-medium">Banner Utama</Text>
                  <div className="mt-2 flex justify-end">
                    <Button size="sm">Unduh</Button>
                  </div>
                </div>
              </div>
              <div className="border border-gray-200 dark:border-gray-700 rounded-md overflow-hidden">
                <img
                  src="https://via.placeholder.com/800x400?text=Promo+Banner+2"
                  alt="Promo Banner 2"
                  className="w-full object-cover"
                />
                <div className="p-3">
                  <Text className="font-medium">Banner Diskon</Text>
                  <div className="mt-2 flex justify-end">
                    <Button size="sm">Unduh</Button>
                  </div>
                </div>
              </div>
              <div className="border border-gray-200 dark:border-gray-700 rounded-md overflow-hidden">
                <img
                  src="https://via.placeholder.com/800x400?text=Promo+Banner+3"
                  alt="Promo Banner 3"
                  className="w-full object-cover"
                />
                <div className="p-3">
                  <Text className="font-medium">Banner Kursus Baru</Text>
                  <div className="mt-2 flex justify-end">
                    <Button size="sm">Unduh</Button>
                  </div>
                </div>
              </div>
            </div>
          </Card>

          <Card className="p-6">
            <Heading level={3} className="mb-4">
              Template Promosi
            </Heading>
            <div className="space-y-4">
              <div className="border border-gray-200 dark:border-gray-700 rounded-md p-4">
                <div className="flex justify-between items-start">
                  <div>
                    <Text className="font-medium">Template Email</Text>
                    <Text className="text-sm text-gray-500 mt-1">
                      Email singkat untuk teman dan keluarga
                    </Text>
                  </div>
                  <Button size="sm">Salin</Button>
                </div>
                <div className="mt-3 bg-gray-50 dark:bg-gray-800 p-3 rounded text-sm">
                  Halo [Nama],<br /><br />
                  Saya ingin merekomendasikan kursus coding yang sangat bagus di
                  CodeCampus. Mereka menawarkan kursus berkualitas dengan harga
                  terjangkau.<br /><br />
                  Gunakan kode referral saya: <strong>{userData.referralCode}</strong> untuk
                  mendapatkan diskon 10% untuk pendaftaran pertama Anda!<br /><br />
                  Link: https://example.com/refer?code={userData.referralCode}<br /><br />
                  Salam,<br />
                  [Nama Anda]
                </div>
              </div>

              <div className="border border-gray-200 dark:border-gray-700 rounded-md p-4">
                <div className="flex justify-between items-start">
                  <div>
                    <Text className="font-medium">Template Post Sosial Media</Text>
                    <Text className="text-sm text-gray-500 mt-1">
                      Untuk diposting di Instagram, Facebook, atau Twitter
                    </Text>
                  </div>
                  <Button size="sm">Salin</Button>
                </div>
                <div className="mt-3 bg-gray-50 dark:bg-gray-800 p-3 rounded text-sm">
                  🚀 Belajar coding jadi lebih mudah dengan CodeCampus!<br /><br />
                  Mereka menawarkan kursus interaktif yang akan mengajarkan Anda
                  keterampilan coding yang dibutuhkan industri. Saya sangat
                  merekomendasikannya untuk pemula maupun yang sudah berpengalaman.<br /><br />
                  Daftar sekarang dengan kode referral saya: <strong>{userData.referralCode}</strong> dan
                  dapatkan diskon 10%!<br /><br />
                  Link: https://example.com/refer?code={userData.referralCode}<br /><br />
                  #BelajarCoding #CodeCampus #WebDevelopment
                </div>
              </div>
            </div>
          </Card>
        </div>
      )}
    </div>
  );
};

export default AffiliateDashboard;

/== organisms/AffiliateForm.tsx
import React, { useState } from "react";
import Button from "../atoms/Button";
import Card from "../atoms/Card";
import Heading from "../atoms/Heading";
import Input from "../atoms/Input";
import TextArea from "../atoms/TextArea";
import Select from "../atoms/Select";
import { FormSection } from "../molecules/FormSection";
import Divider from "../atoms/Divider";

interface AffiliateFormProps {
  initialData?: {
    name: string;
    referralCode: string;
    commission: string;
    cookieDuration: number;
    description: string;
    terms: string;
    status: string;
    paymentSchedule: string;
    multilevel: boolean;
    level2Commission: string;
  };
  onSubmit: (data: any) => void;
  onCancel: () => void;
}

const AffiliateForm: React.FC<AffiliateFormProps> = ({
  initialData,
  onSubmit,
  onCancel,
}) => {
  const [formData, setFormData] = useState(
    initialData || {
      name: "",
      referralCode: "",
      commission: "",
      cookieDuration: 30,
      description: "",
      terms: "",
      status: "active",
      paymentSchedule: "monthly",
      multilevel: false,
      level2Commission: "",
    }
  );

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => {
    const { name, value, type } = e.target;
    
    if (type === 'checkbox') {
      const checkbox = e.target as HTMLInputElement;
      setFormData({ ...formData, [name]: checkbox.checked });
    } else {
      setFormData({ ...formData, [name]: value });
    }
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    // Validate required fields
    if (!formData.name || !formData.referralCode || formData.commission === '') {
      alert('Mohon lengkapi semua kolom yang wajib diisi');
      return;
    }
    
    // Convert any numeric string values to numbers
    const processedData = {
      ...formData,
      commission: formData.commission.toString().replace('%', ''),
      cookieDuration: Number(formData.cookieDuration),
      level2Commission: formData.level2Commission.toString().replace('%', '')
    };
    
    console.log('Submitting form data:', processedData);
    onSubmit(processedData);
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-6 text-gray-800 dark:text-gray-200">
      <Card className="p-6 space-y-6 bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700">
        <FormSection title="Detail Program">
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <Input
              label="Nama Program"
              name="name"
              value={formData.name}
              onChange={handleChange}
              placeholder="Contoh: Program Influencer"
              required
              className="dark:bg-gray-700 dark:border-gray-600"
            />
            <Input
              label="Kode Referral"
              name="referralCode"
              value={formData.referralCode}
              onChange={handleChange}
              placeholder="Contoh: INF2024"
              required
              className="dark:bg-gray-700 dark:border-gray-600"
            />
            <Input
              label="Komisi (%)"
              name="commission"
              value={formData.commission}
              onChange={handleChange}
              placeholder="Contoh: 15"
              type="number"
              min="0"
              max="100"
              required
              className="dark:bg-gray-700 dark:border-gray-600"
            />
            <Input
              label="Masa Aktif Cookie (Hari)"
              name="cookieDuration"
              value={formData.cookieDuration.toString()}
              onChange={handleChange}
              placeholder="30"
              type="number"
              min="1"
              max="365"
              required
              className="dark:bg-gray-700 dark:border-gray-600"
            />
          </div>
          <TextArea
            label="Deskripsi"
            name="description"
            value={formData.description}
            onChange={handleChange}
            placeholder="Deskripsi singkat program afiliasi"
            rows={3}
            className="dark:bg-gray-700 dark:border-gray-600"
          />
          <TextArea
            label="Syarat & Ketentuan"
            name="terms"
            value={formData.terms}
            onChange={handleChange}
            placeholder="Syarat dan ketentuan program"
            rows={5}
            className="dark:bg-gray-700 dark:border-gray-600"
          />
        </FormSection>

        <Divider className="dark:border-gray-700" />

        <FormSection title="Pengaturan Lanjutan">
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <Select
              label="Status"
              name="status"
              value={formData.status}
              onChange={handleChange}
              options={[
                { value: "active", label: "Aktif" },
                { value: "inactive", label: "Tidak Aktif" },
                { value: "pending", label: "Menunggu Persetujuan" },
              ]}
              className="dark:bg-gray-700 dark:border-gray-600"
            />
            <Select
              label="Jadwal Pembayaran"
              name="paymentSchedule"
              value={formData.paymentSchedule}
              onChange={handleChange}
              options={[
                { value: "weekly", label: "Mingguan" },
                { value: "monthly", label: "Bulanan" },
                { value: "quarterly", label: "Per 3 Bulan" },
              ]}
              className="dark:bg-gray-700 dark:border-gray-600"
            />
          </div>

          <div className="flex items-center mt-4">
            <input
              type="checkbox"
              id="multilevel"
              name="multilevel"
              checked={formData.multilevel}
              onChange={handleChange}
              className="h-4 w-4 text-blue-600 rounded border-gray-300 focus:ring-blue-500 dark:border-gray-600 dark:bg-gray-700"
            />
            <label htmlFor="multilevel" className="ml-2 text-sm text-gray-700 dark:text-gray-300">
              Aktifkan Komisi Multi-Level
            </label>
          </div>

          {formData.multilevel && (
            <div className="mt-4">
              <Input
                label="Komisi Level 2 (%)"
                name="level2Commission"
                value={formData.level2Commission}
                onChange={handleChange}
                placeholder="Contoh: 5"
                type="number"
                min="0"
                max="100"
                className="dark:bg-gray-700 dark:border-gray-600"
              />
            </div>
          )}
        </FormSection>

        <div className="flex justify-end space-x-3 pt-4">
          <Button variant="outline" onClick={onCancel} className="dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700">
            Batal
          </Button>
          <Button type="submit" variant="primary">
            Simpan
          </Button>
        </div>
      </Card>
    </form>
  );
};

export default AffiliateForm;

/== organisms/AffiliatePromotionMaterials.tsx
import React, { useState } from "react";
import Card from "../atoms/Card";
import Button from "../atoms/Button";
import Heading from "../atoms/Heading";
import Text from "../atoms/Text";
import IconButton from "../atoms/IconButton";
import Modal from "../atoms/Modal";
import Input from "../atoms/Input";
import TextArea from "../atoms/TextArea";
import Select from "../atoms/Select";
import { Edit, Trash, Copy, Plus, ExternalLink } from "lucide-react";
import { PromotionalMaterial, MaterialType } from "../../store/affiliateStore";

interface AffiliatePromotionMaterialsProps {
  materials: PromotionalMaterial[];
  onAdd: (material: Omit<PromotionalMaterial, "id" | "createdAt" | "updatedAt">) => void;
  onEdit: (id: string, material: Partial<PromotionalMaterial>) => void;
  onDelete: (id: string) => void;
}

const AffiliatePromotionMaterials: React.FC<AffiliatePromotionMaterialsProps> = ({
  materials,
  onAdd,
  onEdit,
  onDelete,
}) => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [currentMaterial, setCurrentMaterial] = useState<PromotionalMaterial | null>(null);
  const [selectedType, setSelectedType] = useState<MaterialType | 'all'>('all');
  const [showCopyToast, setShowCopyToast] = useState(false);
  
  const [formData, setFormData] = useState({
    name: "",
    type: "banner" as MaterialType,
    preview: "",
    description: "",
    programId: "1",
  });

  const filteredMaterials = selectedType === 'all' 
    ? materials 
    : materials.filter(material => material.type === selectedType);

  const handleOpenModal = (material?: PromotionalMaterial) => {
    if (material) {
      setCurrentMaterial(material);
      setFormData({
        name: material.name,
        type: material.type,
        preview: material.preview,
        description: material.description,
        programId: material.programId,
      });
    } else {
      setCurrentMaterial(null);
      setFormData({
        name: "",
        type: "banner",
        preview: "",
        description: "",
        programId: "1",
      });
    }
    setIsModalOpen(true);
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = () => {
    if (currentMaterial) {
      onEdit(currentMaterial.id, formData);
    } else {
      onAdd(formData);
    }
    setIsModalOpen(false);
  };

  const handleCopy = (text: string) => {
    navigator.clipboard.writeText(text);
    setShowCopyToast(true);
    setTimeout(() => setShowCopyToast(false), 3000);
  };

  const getTypeLabel = (type: MaterialType) => {
    switch (type) {
      case 'banner': return 'Banner';
      case 'email': return 'Email';
      case 'text': return 'Teks';
      case 'social': return 'Sosial Media';
      default: return type;
    }
  };

  return (
    <>
      {showCopyToast && (
        <div className="fixed top-4 right-4 bg-green-500 text-white px-4 py-2 rounded-md shadow-lg z-50 animate-fade-in-out">
          Konten berhasil disalin ke clipboard!
        </div>
      )}

      <div className="space-y-6">
        <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
          <Heading level={3} className="text-gray-900 dark:text-white">Materi Promosi</Heading>
          
          <div className="flex flex-wrap gap-2">
            <Button 
              variant="outline" 
              size="sm"
              onClick={() => setSelectedType('all')}
              className={`${selectedType === 'all' ? 'bg-blue-50 text-blue-600 border-blue-200 dark:bg-blue-900 dark:bg-opacity-20 dark:text-blue-400 dark:border-blue-800' : 'dark:text-gray-300 dark:border-gray-700'}`}
            >
              Semua
            </Button>
            <Button 
              variant="outline" 
              size="sm"
              onClick={() => setSelectedType('banner')}
              className={`${selectedType === 'banner' ? 'bg-blue-50 text-blue-600 border-blue-200 dark:bg-blue-900 dark:bg-opacity-20 dark:text-blue-400 dark:border-blue-800' : 'dark:text-gray-300 dark:border-gray-700'}`}
            >
              Banner
            </Button>
            <Button 
              variant="outline" 
              size="sm"
              onClick={() => setSelectedType('email')}
              className={`${selectedType === 'email' ? 'bg-blue-50 text-blue-600 border-blue-200 dark:bg-blue-900 dark:bg-opacity-20 dark:text-blue-400 dark:border-blue-800' : 'dark:text-gray-300 dark:border-gray-700'}`}
            >
              Email
            </Button>
            <Button 
              variant="outline" 
              size="sm"
              onClick={() => setSelectedType('text')}
              className={`${selectedType === 'text' ? 'bg-blue-50 text-blue-600 border-blue-200 dark:bg-blue-900 dark:bg-opacity-20 dark:text-blue-400 dark:border-blue-800' : 'dark:text-gray-300 dark:border-gray-700'}`}
            >
              Teks
            </Button>
            <Button 
              variant="outline" 
              size="sm"
              onClick={() => setSelectedType('social')}
              className={`${selectedType === 'social' ? 'bg-blue-50 text-blue-600 border-blue-200 dark:bg-blue-900 dark:bg-opacity-20 dark:text-blue-400 dark:border-blue-800' : 'dark:text-gray-300 dark:border-gray-700'}`}
            >
              Sosial Media
            </Button>
            
            <Button
              variant="primary"
              size="sm"
              onClick={() => handleOpenModal()}
            >
              <Plus className="w-4 h-4 mr-1" /> Tambah Materi
            </Button>
          </div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          {filteredMaterials.map((material) => (
            <Card key={material.id} className="flex flex-col h-full overflow-hidden dark:bg-gray-800 border border-gray-200 dark:border-gray-700">
              <div className="p-4 flex justify-between items-start">
                <div>
                  <Heading level={4} className="text-gray-900 dark:text-white">{material.name}</Heading>
                  <span className="inline-block px-2 py-1 mt-1 text-xs rounded-full bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300">
                    {getTypeLabel(material.type)}
                  </span>
                </div>
                <div className="flex space-x-1">
                  <IconButton
                    icon={Edit}
                    variant="outline"
                    size="sm"
                    onClick={() => handleOpenModal(material)}
                    title="Edit"
                  />
                  <IconButton
                    icon={Trash}
                    variant="outline"
                    size="sm"
                    onClick={() => onDelete(material.id)}
                    title="Hapus"
                    className="text-red-600 hover:text-red-700 hover:border-red-600"
                  />
                </div>
              </div>
              
              <div className="flex-grow px-4">
                {material.type === "banner" ? (
                  <div className="aspect-w-16 aspect-h-5 bg-gray-100 dark:bg-gray-700 rounded overflow-hidden mb-3">
                    <img 
                      src={material.preview} 
                      alt={material.name} 
                      className="object-cover w-full h-full"
                    />
                  </div>
                ) : (
                  <div className="mb-3 p-3 bg-gray-50 dark:bg-gray-700 rounded-md max-h-32 overflow-auto">
                    <Text className="text-sm text-gray-700 dark:text-gray-300 whitespace-pre-line">
                      {material.preview}
                    </Text>
                  </div>
                )}
              </div>
              
              <div className="px-4 pb-3">
                <Text className="text-sm text-gray-600 dark:text-gray-400 mb-3">{material.description}</Text>
              </div>
              
              <div className="px-4 pb-4 mt-auto">
                {material.type === "banner" ? (
                  <a 
                    href={material.preview} 
                    target="_blank"
                    rel="noopener noreferrer" 
                    className="inline-flex items-center px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-md shadow-sm text-sm font-medium text-gray-700 dark:text-gray-300 bg-white dark:bg-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600 focus:outline-none w-full justify-center"
                  >
                    <ExternalLink className="w-4 h-4 mr-2" />
                    Lihat Full Size
                  </a>
                ) : (
                  <Button 
                    variant="outline" 
                    className="w-full dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700"
                    onClick={() => handleCopy(material.preview)}
                  >
                    <Copy className="w-4 h-4 mr-2" />
                    Salin Konten
                  </Button>
                )}
              </div>
            </Card>
          ))}
        </div>

        {filteredMaterials.length === 0 && (
          <Card className="p-8 text-center dark:bg-gray-800">
            <Text className="text-gray-500 dark:text-gray-400">
              Belum ada materi promosi {selectedType !== 'all' ? `dengan tipe ${getTypeLabel(selectedType)}` : ''}
            </Text>
            <Button
              variant="primary"
              className="mt-4"
              onClick={() => handleOpenModal()}
            >
              <Plus className="w-4 h-4 mr-1" /> Tambah Materi Promosi
            </Button>
          </Card>
        )}
      </div>

      <Modal 
        open={isModalOpen} 
        onClose={() => setIsModalOpen(false)}
        title={currentMaterial ? "Edit Materi Promosi" : "Tambah Materi Promosi"}
        size="lg"
      >
        <div className="space-y-4">
          <Input
            label="Nama Materi"
            name="name"
            value={formData.name}
            onChange={handleChange}
            placeholder="Contoh: Banner Utama"
            required
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          <Select
            label="Tipe Materi"
            name="type"
            value={formData.type}
            onChange={handleChange}
            options={[
              { value: "banner", label: "Banner" },
              { value: "email", label: "Template Email" },
              { value: "text", label: "Teks Promosi" },
              { value: "social", label: "Konten Sosial Media" },
            ]}
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          <Select
            label="Program Afiliasi"
            name="programId"
            value={formData.programId}
            onChange={handleChange}
            options={[
              { value: "1", label: "Program Influencer" },
              { value: "2", label: "Program Kampus" },
              { value: "3", label: "Program Komunitas" },
            ]}
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          {formData.type === "banner" ? (
            <Input
              label="URL Gambar"
              name="preview"
              value={formData.preview}
              onChange={handleChange}
              placeholder="https://example.com/banner.jpg"
              className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
            />
          ) : (
            <TextArea
              label="Konten"
              name="preview"
              value={formData.preview}
              onChange={handleChange}
              placeholder="Masukkan konten promosi di sini..."
              rows={4}
              className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
            />
          )}
          
          <TextArea
            label="Deskripsi"
            name="description"
            value={formData.description}
            onChange={handleChange}
            placeholder="Deskripsi singkat tentang materi promosi ini..."
            rows={2}
            className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          />
          
          <div className="flex justify-end space-x-2 pt-2">
            <Button 
              variant="outline" 
              onClick={() => setIsModalOpen(false)}
              className="dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700" 
            >
              Batal
            </Button>
            <Button variant="primary" onClick={handleSubmit}>Simpan</Button>
          </div>
        </div>
      </Modal>
    </>
  );
};

export default AffiliatePromotionMaterials;

/== organisms/AffiliateSettings.tsx
import React, { useState } from 'react';
import Card from '../atoms/Card';
import Heading from '../atoms/Heading';
import Text from '../atoms/Text';
import Button from '../atoms/Button';
import Input from '../atoms/Input';
import TextArea from '../atoms/TextArea';
import Divider from '../atoms/Divider';
import { FormSection } from '../molecules/FormSection';
import { AffiliateSettings as AffiliateSettingsType } from '../../store/affiliateStore';

interface AffiliateSettingsProps {
  settings: AffiliateSettingsType;
  onSave: (settings: AffiliateSettingsType) => void;
}

const AffiliateSettings: React.FC<AffiliateSettingsProps> = ({ settings, onSave }) => {
  const [formData, setFormData] = useState<AffiliateSettingsType>(settings);
  const [isEditing, setIsEditing] = useState(false);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => {
    const { name, value, type, checked } = e.target as HTMLInputElement;
    
    if (name.includes('.')) {
      const [section, key] = name.split('.');
      if (type === 'checkbox') {
        setFormData({
          ...formData,
          [section]: {
            ...formData[section as keyof AffiliateSettingsType],
            [key]: checked,
          },
        });
      } else {
        setFormData({
          ...formData,
          [section]: {
            ...formData[section as keyof AffiliateSettingsType],
            [key]: type === 'number' ? parseFloat(value) : value,
          },
        });
      }
    } else if (name.includes('[') && name.includes(']')) {
      const pattern = /(\w+)\[(\w+)\]\.(\w+)/;
      const matches = name.match(pattern);
      
      if (matches) {
        const [_, section, level, property] = matches;
        setFormData({
          ...formData,
          [section]: {
            ...formData[section as keyof AffiliateSettingsType],
            levels: {
              ...formData.levelSettings.levels,
              [level]: {
                ...formData.levelSettings.levels[level as keyof typeof formData.levelSettings.levels],
                [property]: type === 'number' ? parseFloat(value) : value,
              },
            },
          },
        });
      }
    } else {
      setFormData({
        ...formData,
        [name]: type === 'checkbox' ? checked : (type === 'number' ? parseFloat(value) : value),
      });
    }
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSave(formData);
    setIsEditing(false);
  };

  return (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <Heading level={3} className="text-gray-900 dark:text-white">Pengaturan Program Afiliasi</Heading>
        {!isEditing ? (
          <Button onClick={() => setIsEditing(true)}>Edit Pengaturan</Button>
        ) : (
          <div className="flex space-x-2">
            <Button variant="outline" onClick={() => setIsEditing(false)} className="dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700">Batal</Button>
            <Button onClick={handleSubmit}>Simpan Perubahan</Button>
          </div>
        )}
      </div>

      <form onSubmit={handleSubmit}>
        <div className="space-y-6">
          {/* Global Settings */}
          <Card className="p-6 dark:bg-gray-800">
            <FormSection title="Pengaturan Umum">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="globalSettings.enabled"
                    name="globalSettings.enabled"
                    checked={formData.globalSettings.enabled}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="globalSettings.enabled"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Aktifkan Program Afiliasi
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="globalSettings.multilevelEnabled"
                    name="globalSettings.multilevelEnabled"
                    checked={formData.globalSettings.multilevelEnabled}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="globalSettings.multilevelEnabled"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Aktifkan Sistem Multi-Level
                  </label>
                </div>
              </div>

              <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
                <Input
                  type="number"
                  label="Komisi Default (%)"
                  name="globalSettings.defaultCommission"
                  value={formData.globalSettings.defaultCommission.toString()}
                  onChange={handleChange}
                  disabled={!isEditing}
                  min="0"
                  max="100"
                  className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                />

                <Input
                  type="number"
                  label="Durasi Cookie Default (hari)"
                  name="globalSettings.defaultCookieDuration"
                  value={formData.globalSettings.defaultCookieDuration.toString()}
                  onChange={handleChange}
                  disabled={!isEditing}
                  min="1"
                  max="365"
                  className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                />

                <Input
                  type="number"
                  label="Minimum Penarikan Dana (Rp)"
                  name="globalSettings.minPayoutAmount"
                  value={formData.globalSettings.minPayoutAmount.toString()}
                  onChange={handleChange}
                  disabled={!isEditing}
                  min="0"
                  className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                />

                <div className="w-full">
                  <label className="block mb-2 text-sm font-medium text-gray-900 dark:text-white">
                    Jadwal Pembayaran Default
                  </label>
                  <select
                    name="globalSettings.defaultPaymentSchedule"
                    value={formData.globalSettings.defaultPaymentSchedule}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                  >
                    <option value="weekly">Mingguan</option>
                    <option value="monthly">Bulanan</option>
                    <option value="quarterly">Per 3 Bulan</option>
                  </select>
                </div>
              </div>

              <div className="mt-4">
                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="globalSettings.applicationRequiresApproval"
                    name="globalSettings.applicationRequiresApproval"
                    checked={formData.globalSettings.applicationRequiresApproval}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="globalSettings.applicationRequiresApproval"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Pendaftaran Memerlukan Persetujuan
                  </label>
                </div>
              </div>
            </FormSection>
          </Card>

          {/* Payment Methods */}
          <Card className="p-6 dark:bg-gray-800">
            <FormSection title="Metode Pembayaran">
              <div className="space-y-3">
                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="paymentMethods.bankTransfer"
                    name="paymentMethods.bankTransfer"
                    checked={formData.paymentMethods.bankTransfer}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="paymentMethods.bankTransfer"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Bank Transfer
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="paymentMethods.eWallet"
                    name="paymentMethods.eWallet"
                    checked={formData.paymentMethods.eWallet}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="paymentMethods.eWallet"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    E-Wallet (GoPay, OVO, DANA)
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="paymentMethods.paypal"
                    name="paymentMethods.paypal"
                    checked={formData.paymentMethods.paypal}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="paymentMethods.paypal"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    PayPal
                  </label>
                </div>
              </div>
            </FormSection>
          </Card>

          {/* Level Settings */}
          <Card className="p-6 dark:bg-gray-800">
            <FormSection title="Pengaturan Level Afiliator">
              <div className="mb-4">
                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="levelSettings.enabled"
                    name="levelSettings.enabled"
                    checked={formData.levelSettings.enabled}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="levelSettings.enabled"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Aktifkan Sistem Level
                  </label>
                </div>
              </div>

              {formData.levelSettings.enabled && (
                <div className="space-y-4">
                  <div className="grid grid-cols-12 gap-4 text-sm font-medium text-gray-700 dark:text-gray-300">
                    <div className="col-span-4">Level</div>
                    <div className="col-span-4">Minimum Referral</div>
                    <div className="col-span-4">Bonus Komisi (%)</div>
                  </div>
                  
                  <div className="grid grid-cols-12 gap-4 items-center">
                    <div className="col-span-4">
                      <div className="flex items-center">
                        <div className="h-5 w-5 bg-amber-700 rounded-full mr-2"></div>
                        <Text className="dark:text-gray-300">Bronze</Text>
                      </div>
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[bronze].minReferrals"
                        value={formData.levelSettings.levels.bronze.minReferrals.toString()}
                        onChange={handleChange}
                        disabled={!isEditing || true} // Bronze level min refs should always be 0
                        min="0"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[bronze].bonusCommission"
                        value={formData.levelSettings.levels.bronze.bonusCommission.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="0"
                        max="100"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                  </div>

                  <div className="grid grid-cols-12 gap-4 items-center">
                    <div className="col-span-4">
                      <div className="flex items-center">
                        <div className="h-5 w-5 bg-gray-400 rounded-full mr-2"></div>
                        <Text className="dark:text-gray-300">Silver</Text>
                      </div>
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[silver].minReferrals"
                        value={formData.levelSettings.levels.silver.minReferrals.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="1"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[silver].bonusCommission"
                        value={formData.levelSettings.levels.silver.bonusCommission.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="0"
                        max="100"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                  </div>

                  <div className="grid grid-cols-12 gap-4 items-center">
                    <div className="col-span-4">
                      <div className="flex items-center">
                        <div className="h-5 w-5 bg-yellow-500 rounded-full mr-2"></div>
                        <Text className="dark:text-gray-300">Gold</Text>
                      </div>
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[gold].minReferrals"
                        value={formData.levelSettings.levels.gold.minReferrals.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="1"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[gold].bonusCommission"
                        value={formData.levelSettings.levels.gold.bonusCommission.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="0"
                        max="100"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                  </div>

                  <div className="grid grid-cols-12 gap-4 items-center">
                    <div className="col-span-4">
                      <div className="flex items-center">
                        <div className="h-5 w-5 bg-blue-300 rounded-full mr-2"></div>
                        <Text className="dark:text-gray-300">Platinum</Text>
                      </div>
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[platinum].minReferrals"
                        value={formData.levelSettings.levels.platinum.minReferrals.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="1"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                    <div className="col-span-4">
                      <Input
                        type="number"
                        name="levelSettings[platinum].bonusCommission"
                        value={formData.levelSettings.levels.platinum.bonusCommission.toString()}
                        onChange={handleChange}
                        disabled={!isEditing}
                        min="0"
                        max="100"
                        className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                      />
                    </div>
                  </div>
                </div>
              )}
            </FormSection>
          </Card>

          {/* Form Fields Settings */}
          <Card className="p-6 dark:bg-gray-800">
            <FormSection title="Kolom Formulir Pendaftaran">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="applicationFormFields.website"
                    name="applicationFormFields.website"
                    checked={formData.applicationFormFields.website}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="applicationFormFields.website"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Website
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="applicationFormFields.phone"
                    name="applicationFormFields.phone"
                    checked={formData.applicationFormFields.phone}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="applicationFormFields.phone"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Nomor Telepon
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="applicationFormFields.socialMedia"
                    name="applicationFormFields.socialMedia"
                    checked={formData.applicationFormFields.socialMedia}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="applicationFormFields.socialMedia"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Sosial Media
                  </label>
                </div>

                <div className="flex items-center">
                  <input
                    type="checkbox"
                    id="applicationFormFields.marketingPlan"
                    name="applicationFormFields.marketingPlan"
                    checked={formData.applicationFormFields.marketingPlan}
                    onChange={handleChange}
                    disabled={!isEditing}
                    className="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                  />
                  <label
                    htmlFor="applicationFormFields.marketingPlan"
                    className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300"
                  >
                    Rencana Pemasaran
                  </label>
                </div>
              </div>
            </FormSection>
          </Card>

          {/* Terms & Conditions */}
          <Card className="p-6 dark:bg-gray-800">
            <FormSection title="Syarat & Ketentuan">
              <TextArea
                name="termsAndConditions"
                value={formData.termsAndConditions}
                onChange={handleChange}
                disabled={!isEditing}
                rows={10}
                className="dark:bg-gray-700 dark:border-gray-600 dark:text-white"
              />
            </FormSection>
          </Card>

          {isEditing && (
            <div className="flex justify-end mt-6">
              <Button type="submit">Simpan Semua Perubahan</Button>
            </div>
          )}
        </div>
      </form>
    </div>
  );
};

export default AffiliateSettings;

/== organisms/AffiliatorList.tsx
import React, { useState } from 'react';
import Badge from '../atoms/Badge';
import Button from '../atoms/Button';
import Card from '../atoms/Card';
import Heading from '../atoms/Heading';
import IconButton from '../atoms/IconButton';
import Text from '../atoms/Text';
import { Eye, Edit, Trash, Check, X, ExternalLink } from 'lucide-react';
import { Affiliator, AffiliateStatus } from '../../store/affiliateStore';

interface AffiliatorListProps {
  affiliators: Affiliator[];
  onViewAffiliator: (id: string) => void;
  onEditAffiliator: (id: string) => void;
  onDeleteAffiliator: (id: string) => void;
  onApproveAffiliator: (id: string) => void;
  onRejectAffiliator: (id: string) => void;
}

const AffiliatorList: React.FC<AffiliatorListProps> = ({
  affiliators,
  onViewAffiliator,
  onEditAffiliator,
  onDeleteAffiliator,
  onApproveAffiliator,
  onRejectAffiliator,
}) => {
  const [filter, setFilter] = useState<AffiliateStatus | 'all'>('all');
  const [searchTerm, setSearchTerm] = useState('');

  const filteredAffiliators = affiliators
    .filter(affiliator => filter === 'all' || affiliator.status === filter)
    .filter(affiliator => 
      affiliator.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
      affiliator.email.toLowerCase().includes(searchTerm.toLowerCase()) ||
      affiliator.referralCode.toLowerCase().includes(searchTerm.toLowerCase())
    );

  const getStatusBadge = (status: AffiliateStatus) => {
    switch (status) {
      case 'active':
        return <Badge color="green">Aktif</Badge>;
      case 'pending':
        return <Badge color="yellow">Menunggu</Badge>;
      case 'inactive':
        return <Badge color="red">Tidak Aktif</Badge>;
      case 'rejected':
        return <Badge color="red">Ditolak</Badge>;
      default:
        return <Badge color="default">Unknown</Badge>;
    }
  };

  const getLevelBadge = (level: string) => {
    switch (level) {
      case 'bronze':
        return <Badge className="bg-amber-700 text-white dark:bg-amber-800">Bronze</Badge>;
      case 'silver':
        return <Badge className="bg-gray-400 text-white dark:bg-gray-500">Silver</Badge>;
      case 'gold':
        return <Badge className="bg-yellow-500 text-white dark:bg-yellow-600">Gold</Badge>;
      case 'platinum':
        return <Badge className="bg-blue-300 text-gray-800 dark:bg-blue-400">Platinum</Badge>;
      default:
        return <Badge color="default">Unknown</Badge>;
    }
  };

  return (
    <div className="space-y-4">
      <div className="flex flex-col md:flex-row justify-between gap-4">
        <div className="w-full md:w-72">
          <div className="relative">
            <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
              <svg className="w-4 h-4 text-gray-500 dark:text-gray-400" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">
                <path stroke="currentColor" strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>
              </svg>
            </div>
            <input 
              type="search" 
              className="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-white focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
              placeholder="Cari nama, email, kode..."
              value={searchTerm}
              onChange={(e) => setSearchTerm(e.target.value)}
            />
          </div>
        </div>
        <div className="flex flex-wrap gap-2">
          <Button 
            variant={filter === 'all' ? 'primary' : 'outline'} 
            size="sm"
            onClick={() => setFilter('all')}
          >
            Semua
          </Button>
          <Button 
            variant={filter === 'active' ? 'primary' : 'outline'} 
            size="sm"
            onClick={() => setFilter('active')}
          >
            Aktif
          </Button>
          <Button 
            variant={filter === 'pending' ? 'primary' : 'outline'} 
            size="sm"
            onClick={() => setFilter('pending')}
          >
            Menunggu
          </Button>
          <Button 
            variant={filter === 'inactive' ? 'primary' : 'outline'} 
            size="sm"
            onClick={() => setFilter('inactive')}
          >
            Tidak Aktif
          </Button>
          <Button 
            variant={filter === 'rejected' ? 'primary' : 'outline'} 
            size="sm"
            onClick={() => setFilter('rejected')}
          >
            Ditolak
          </Button>
        </div>
      </div>

      <div className="overflow-x-auto relative shadow-md sm:rounded-lg">
        <table className="w-full text-sm text-left text-gray-500 dark:text-gray-400">
          <thead className="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
              <th scope="col" className="py-3 px-6">Nama</th>
              <th scope="col" className="py-3 px-6">Email</th>
              <th scope="col" className="py-3 px-6">Kode Referral</th>
              <th scope="col" className="py-3 px-6">Level</th>
              <th scope="col" className="py-3 px-6">Referral</th>
              <th scope="col" className="py-3 px-6">Konversi</th>
              <th scope="col" className="py-3 px-6">Penghasilan</th>
              <th scope="col" className="py-3 px-6">Status</th>
              <th scope="col" className="py-3 px-6">Aksi</th>
            </tr>
          </thead>
          <tbody>
            {filteredAffiliators.map((affiliator) => (
              <tr key={affiliator.id} className="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-700">
                <td className="py-4 px-6 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                  <div className="flex items-center">
                    {affiliator.profilePicture ? (
                      <img
                        src={affiliator.profilePicture}
                        alt={affiliator.name}
                        className="w-8 h-8 rounded-full mr-3"
                      />
                    ) : (
                      <div className="w-8 h-8 rounded-full bg-blue-100 dark:bg-blue-900 text-blue-600 dark:text-blue-300 flex items-center justify-center mr-3">
                        {affiliator.name.charAt(0).toUpperCase()}
                      </div>
                    )}
                    {affiliator.name}
                  </div>
                </td>
                <td className="py-4 px-6">{affiliator.email}</td>
                <td className="py-4 px-6">
                  <Badge color="blue">{affiliator.referralCode}</Badge>
                </td>
                <td className="py-4 px-6">
                  {getLevelBadge(affiliator.level)}
                </td>
                <td className="py-4 px-6">{affiliator.totalReferrals}</td>
                <td className="py-4 px-6">{affiliator.totalConversions}</td>
                <td className="py-4 px-6">Rp {affiliator.totalEarnings.toLocaleString()}</td>
                <td className="py-4 px-6">
                  {getStatusBadge(affiliator.status)}
                </td>
                <td className="py-4 px-6">
                  <div className="flex items-center space-x-2">
                    <IconButton
                      icon={Eye}
                      variant="outline"
                      onClick={() => onViewAffiliator(affiliator.id)}
                      title="Lihat Detail"
                      size="sm"
                    />
                    <IconButton
                      icon={Edit}
                      variant="outline"
                      onClick={() => onEditAffiliator(affiliator.id)}
                      title="Edit"
                      size="sm"
                    />
                    {affiliator.status === 'pending' && (
                      <>
                        <IconButton
                          icon={Check}
                          variant="outline"
                          color="primary"
                          onClick={() => onApproveAffiliator(affiliator.id)}
                          title="Setujui"
                          size="sm"
                          className="text-green-600 hover:text-green-700 hover:border-green-600"
                        />
                        <IconButton
                          icon={X}
                          variant="outline"
                          color="primary"
                          onClick={() => onRejectAffiliator(affiliator.id)}
                          title="Tolak"
                          size="sm"
                          className="text-red-600 hover:text-red-700 hover:border-red-600"
                        />
                      </>
                    )}
                    <IconButton
                      icon={Trash}
                      variant="outline"
                      onClick={() => onDeleteAffiliator(affiliator.id)}
                      title="Hapus"
                      size="sm"
                      className="text-red-600 hover:text-red-700 hover:border-red-600"
                    />
                  </div>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {filteredAffiliators.length === 0 && (
        <div className="text-center py-10">
          <Text className="text-gray-500 dark:text-gray-400">Tidak ada data yang sesuai dengan filter</Text>
        </div>
      )}
    </div>
  );
};

export default AffiliatorList;

/== organisms/BundleForm.tsx
import React, { useState, useEffect } from 'react';
import { Bundle, Course } from '../../store/useBundleStore';
import { FormSection } from '../molecules/FormSection';
import  Input  from '../atoms/Input';
import  TextArea  from '../atoms/TextArea';
import  Select  from '../atoms/Select';
import  Button  from '../atoms/Button';
import  CourseCard  from '../molecules/CourseCard';
import { CourseSortable } from '../molecules/CourseSortable';

interface BundleFormProps {
  courses: Course[];
  initialData?: Partial<Bundle>;
  onSubmit: (data: Omit<Bundle, 'id' | 'createdAt' | 'updatedAt'>) => void;
  isLoading?: boolean;
}

export const BundleForm: React.FC<BundleFormProps> = ({
  courses,
  initialData,
  onSubmit,
  isLoading,
}) => {
  const [formData, setFormData] = useState<Partial<Bundle>>({
    title: '',
    description: '',
    courses: [],
    originalPrice: 0,
    bundlePrice: 0,
    discountPercentage: 0,
    theme: '',
    difficultyLevel: 'beginner',
    limitedTime: false,
    accessDuration: 'lifetime',
    hasCertificate: false,
    isActive: true,
    ...initialData,
  });
  
  const [errors, setErrors] = useState<Record<string, string>>({});
  
  // Calculate original price based on selected courses
  useEffect(() => {
    if (formData.courses && formData.courses.length > 0) {
      const selectedCourses = courses.filter(course => 
        formData.courses?.includes(course.id)
      );
      
      const total = selectedCourses.reduce((sum, course) => sum + course.price, 0);
      
      setFormData(prev => ({
        ...prev,
        originalPrice: total,
      }));
      
      // If discount percentage is set, recalculate bundle price
      if (formData.discountPercentage) {
        const discountedPrice = total * (1 - (formData.discountPercentage / 100));
        setFormData(prev => ({
          ...prev,
          bundlePrice: Math.round(discountedPrice * 100) / 100,
        }));
      }
    }
  }, [formData.courses, formData.discountPercentage, courses]);
  
  const handleCourseSelect = (courseId: string) => {
    setFormData(prev => {
      const newCourses = prev.courses ? [...prev.courses] : [];
      
      if (newCourses.includes(courseId)) {
        return {
          ...prev,
          courses: newCourses.filter(id => id !== courseId),
        };
      } else {
        return {
          ...prev,
          courses: [...newCourses, courseId],
        };
      }
    });
  };
  
  const handleDiscountChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const discount = parseFloat(e.target.value);
    
    if (formData.originalPrice) {
      const discountedPrice = formData.originalPrice * (1 - (discount / 100));
      
      setFormData(prev => ({
        ...prev,
        discountPercentage: discount,
      }));
    }
  };
  
  const handleReorderCourses = (newOrder: string[]) => {
    setFormData(prev => ({
      ...prev,
      courses: newOrder,
      courseOrder: newOrder,
    }));
  };
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    // Validate form
    const newErrors: Record<string, string> = {};
    
    if (!formData.title) {
      newErrors.title = 'Title is required';
    }
    
    if (!formData.description) {
      newErrors.description = 'Description is required';
    }
    
    if (!formData.courses || formData.courses.length === 0) {
      newErrors.courses = 'At least one course must be selected';
    }
    
    if (!formData.theme) {
      newErrors.theme = 'Theme is required';
    }
    
    if (formData.limitedTime) {
      if (!formData.startDate) {
        newErrors.startDate = 'Start date is required for limited time bundles';
      }
      
      if (!formData.endDate) {
        newErrors.endDate = 'End date is required for limited time bundles';
      }
      
      if (formData.startDate && formData.endDate && new Date(formData.startDate) >= new Date(formData.endDate)) {
        newErrors.endDate = 'End date must be after start date';
      }
    }
    
    setErrors(newErrors);
    
    if (Object.keys(newErrors).length === 0) {
      onSubmit(formData as Omit<Bundle, 'id' | 'createdAt' | 'updatedAt'>);
    }
  };
  
  return (
    <form onSubmit={handleSubmit} className="space-y-8">
      <FormSection title="Basic Information" description="Enter the main details for your bundle">
        <Input
          label="Bundle Title"
          value={formData.title}
          onChange={(e) => setFormData({...formData, title: e.target.value})}
          placeholder="e.g., Web Development Mastery Bundle"
          error={errors.title}
          required
        />
        
        <TextArea
          label="Bundle Description"
          value={formData.description}
          onChange={(e) => setFormData({...formData, description: e.target.value})}
          placeholder="Describe the bundle and its benefits..."
          rows={4}
          error={errors.description}
          required
        />
        
        <Select
          label="Difficulty Level"
          value={formData.difficultyLevel}
          onChange={(value) => setFormData({...formData, difficultyLevel: value as 'beginner' | 'intermediate' | 'advanced'})}
          options={[
            { value: 'beginner', label: 'Beginner' },
            { value: 'intermediate', label: 'Intermediate' },
            { value: 'advanced', label: 'Advanced' },
          ]}
        />
        
        <Input
          label="Theme"
          value={formData.theme}
          onChange={(e) => setFormData({...formData, theme: e.target.value})}
          placeholder="e.g., Web Development, Data Science, Business"
          error={errors.theme}
          required
        />
        
        <Input
          label="Career Path (Optional)"
          value={formData.careerPath || ''}
          onChange={(e) => setFormData({...formData, careerPath: e.target.value})}
          placeholder="e.g., Frontend Developer, Data Analyst"
        />
      </FormSection>
      
      <FormSection title="Course Selection" description="Select courses to include in this bundle">
        {errors.courses && (
          <p className="text-red-600 text-sm mb-3">{errors.courses}</p>
        )}
        
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          {courses.map((course) => (
            <CourseCard
              key={course.id}
              course={course}
              isSelected={formData.courses?.includes(course.id)}
              onClick={() => handleCourseSelect(course.id)}
            />
          ))}
        </div>
        
        {formData.courses && formData.courses.length > 0 && (
          <div className="mt-6">
            <CourseSortable
              courses={courses}
              selectedCourseIds={formData.courses}
              onReorder={handleReorderCourses}
            />
          </div>
        )}
      </FormSection>
      
      <FormSection title="Pricing" description="Set the pricing options for this bundle">
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div>
            <Input
              label="Original Price"
              value={formData.originalPrice?.toString()}
              disabled
              readOnly
              prefix="$"
            />
            <p className="text-xs text-gray-500 mt-1">
              Calculated from the sum of individual course prices
            </p>
          </div>
          
          <div>
            <Input
              label="Discount Percentage"
              type="number"
              min="0"
              max="100"
              value={formData.discountPercentage?.toString()}
              onChange={handleDiscountChange}
              suffix="%"
            />
          </div>
          
          <div>
            <Input
              label="Bundle Price"
              type="number"
              min="0"
              step="0.01"
              value={formData.bundlePrice?.toString()}
              onChange={(e) => setFormData({...formData, bundlePrice: parseFloat(e.target.value)})}
              prefix="$"
            />
            <p className="text-xs text-gray-500 mt-1">
              You can manually adjust this price or set a discount percentage
            </p>
          </div>
        </div>
      </FormSection>
      
      <FormSection title="Availability" description="Set when and how this bundle is available">
        <div className="flex items-center mb-4">
          <input
            type="checkbox"
            id="limitedTime"
            checked={formData.limitedTime}
            onChange={(e) => setFormData({...formData, limitedTime: e.target.checked})}
            className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
          />
          <label htmlFor="limitedTime" className="ml-2 block text-sm text-gray-700">
            Limited Time Bundle
          </label>
        </div>
        
        {formData.limitedTime && (
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <Input
              label="Start Date"
              type="date"
              value={formData.startDate instanceof Date ? formData.startDate.toISOString().split('T')[0] : formData.startDate}
              onChange={(e) => setFormData({...formData, startDate: new Date(e.target.value)})}
              error={errors.startDate}
            />
            
            <Input
              label="End Date"
              type="date"
              value={formData.endDate instanceof Date ? formData.endDate.toISOString().split('T')[0] : formData.endDate}
              onChange={(e) => setFormData({...formData, endDate: new Date(e.target.value)})}
              error={errors.endDate}
            />
          </div>
        )}
        
        <div className="mt-4">
          <Select
            label="Access Duration"
            value={formData.accessDuration === 'lifetime' ? 'lifetime' : formData.accessDuration?.toString()}
            onChange={(value) => setFormData({
              ...formData, 
              accessDuration: value === 'lifetime' ? 'lifetime' : parseInt(value)
            })}
            options={[
              { value: 'lifetime', label: 'Lifetime Access' },
              { value: '30', label: '30 days' },
              { value: '60', label: '60 days' },
              { value: '90', label: '90 days' },
              { value: '180', label: '6 months' },
              { value: '365', label: '1 year' },
            ]}
          />
        </div>
      </FormSection>
      
      <FormSection title="Additional Options" description="Configure additional bundle settings">
        <div className="flex items-center mb-4">
          <input
            type="checkbox"
            id="hasCertificate"
            checked={formData.hasCertificate}
            onChange={(e) => setFormData({...formData, hasCertificate: e.target.checked})}
            className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
          />
          <label htmlFor="hasCertificate" className="ml-2 block text-sm text-gray-700">
            Bundle Certificate
          </label>
          <p className="ml-2 text-xs text-gray-500">
            Offer a special certificate for completing the entire bundle
          </p>
        </div>
        
        <div className="flex items-center">
          <input
            type="checkbox"
            id="isActive"
            checked={formData.isActive}
            onChange={(e) => setFormData({...formData, isActive: e.target.checked})}
            className="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
          />
          <label htmlFor="isActive" className="ml-2 block text-sm text-gray-700">
            Active Bundle
          </label>
          <p className="ml-2 text-xs text-gray-500">
            Make this bundle visible and available for purchase
          </p>
        </div>
      </FormSection>
      
      <div className="flex justify-end">
        <Button type="submit" isLoading={isLoading}>
          {initialData?.id ? 'Update Bundle' : 'Create Bundle'}
        </Button>
      </div>
    </form>
  );
};

/== organisms/BundleStatistics.tsx
import React from 'react';
import { BundleStats } from '../../store/useBundleStore';
import { ProgressStats } from '../molecules/ProgressStats';

interface BundleStatisticsProps {
  stats: BundleStats;
  isLoading?: boolean;
}

export const BundleStatistics: React.FC<BundleStatisticsProps> = ({
  stats,
  isLoading,
}) => {
  if (isLoading) {
    return (
      <div className="flex justify-center items-center h-48">
        <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
      </div>
    );
  }

  const { purchases, completionRate, averageCompletionTime, revenue } = stats;

  return (
    <div className="bg-white dark:bg-dark-800 rounded-lg shadow-sm border p-4">
      <h3 className="text-lg font-medium text-gray-900 mb-4">Bundle Performance</h3>
      
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <div className="bg-blue-50 rounded-lg p-4">
          <div className="text-sm text-blue-600 font-medium">Total Purchases</div>
          <div className="text-3xl font-bold text-blue-800">{purchases}</div>
        </div>
        
        <div className="bg-green-50 rounded-lg p-4">
          <div className="text-sm text-green-600 font-medium">Total Revenue</div>
          <div className="text-3xl font-bold text-green-800">${revenue.toFixed(2)}</div>
        </div>
      </div>
      
      <div className="space-y-4">
        <ProgressStats
          title="Completion Rate"
          value={Math.round(completionRate)}
          max={100}
          percentage={Math.round(completionRate)}
          color="blue"
        />
        
        <div className="bg-white dark:bg-dark-800 rounded-lg p-4 border">
          <h4 className="text-sm font-medium text-gray-500 mb-1">Average Completion Time</h4>
          <div className="text-2xl font-bold">
            {averageCompletionTime} days
          </div>
        </div>
      </div>
    </div>
  );
};

/== organisms/BundlesList.tsx
import React from 'react';
import { Bundle } from '../../store/useBundleStore';
import { BundleCard } from '../molecules/BundleCard';

interface BundlesListProps {
  bundles: Bundle[];
  onEdit: (id: string) => void;
  onDelete: (id: string) => void;
  onView: (id: string) => void;
  isLoading?: boolean;
}

export const BundlesList: React.FC<BundlesListProps> = ({
  bundles,
  onEdit,
  onDelete,
  onView,
  isLoading,
}) => {
  if (isLoading) {
    return (
      <div className="flex justify-center items-center h-64">
        <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
      </div>
    );
  }

  if (bundles.length === 0) {
    return (
      <div className="bg-gray-800 dark:bg-dark-800 p-6 rounded-lg border text-center">
        <h3 className="text-lg font-medium text-gray-900 dark:text-white mb-2">No bundles found</h3>
        <p className="text-gray-500  dark:text-white">Create your first bundle to get started</p>
      </div>
    );
  }

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {bundles.map((bundle) => (
        <BundleCard
          key={bundle.id}
          bundle={bundle}
          onEdit={() => onEdit(bundle.id)}
          onDelete={() => onDelete(bundle.id)}
          onView={() => onView(bundle.id)}
        />
      ))}
    </div>
  );
};

/== organisms/CodeWorkspace.tsx
import React from 'react';
import { Play, Download, Copy, Trash, Settings, Maximize2 } from 'lucide-react';
import Button from '../atoms/Button';
import CodeEditor from '../molecules/CodeEditor';

interface CodeWorkspaceProps {
  code: string;
  output: string;
  isRunning: boolean;
  consoleExpanded: boolean;
  onCodeChange: (value: string | undefined) => void;
  onRun: () => void;
  onCopy: () => void;
  onClear: () => void;
  onDownload: () => void;
  onToggleConsole: () => void;
  theme?: string;
  fontSize?: string;
}

const CodeWorkspace: React.FC<CodeWorkspaceProps> = ({
  code,
  output,
  isRunning,
  consoleExpanded,
  onCodeChange,
  onRun,
  onCopy,
  onClear,
  onDownload,
  onToggleConsole,
  theme = 'vs-dark',
  fontSize = '14px'
}) => {
  return (
    <div className="flex flex-col h-full bg-gray-900 text-white">
      {/* Editor Header */}
      <div className="flex items-center justify-between p-2 border-b border-gray-700">
        <div className="flex items-center">
          <span className="text-sm font-medium">main.py</span>
        </div>
        <div className="flex items-center space-x-2">
          <Button
            variant="primary"
            icon={Play}
            onClick={onRun}
            disabled={isRunning}
            size="sm"
          >
            {isRunning ? 'Running...' : 'Run'}
          </Button>
          <Button
            variant="outline"
            icon={Copy}
            onClick={onCopy}
            size="sm"
            title="Copy Code"
          />
          <Button
            variant="outline"
            icon={Download}
            onClick={onDownload}
            size="sm"
            title="Download Code"
          />
          <Button
            variant="outline"
            icon={Trash}
            onClick={onClear}
            size="sm"
            title="Clear Code"
          />
        </div>
      </div>
      
      {/* Code Editor */}
      <div className="flex-1">
        <CodeEditor
          code={code}
          onChange={onCodeChange}
          language="python"
          theme={theme}
          fontSize={fontSize}
        />
      </div>
      
      {/* Console Output */}
      <div className={`border-t border-gray-700 ${consoleExpanded ? 'h-1/2' : 'h-40'}`}>
        <div className="flex items-center justify-between p-2 bg-gray-800">
          <div className="text-sm font-medium">Console</div>
          <Button
            variant="outline"
            icon={Maximize2}
            onClick={onToggleConsole}
            size="sm"
          />
        </div>
        <div className="p-4 font-mono text-sm overflow-auto h-[calc(100%-36px)]">
          {output || 'Run your code to see the output here...'}
        </div>
      </div>
    </div>
  );
};

export default CodeWorkspace;

/== organisms/CourseList.tsx
import React from 'react';
import CourseCard from '../molecules/CourseCard';
import { Course } from '../../store/courseStore';

interface CourseListProps {
  courses: Course[];
  title?: string;
  description?: string;
}

const CourseList: React.FC<CourseListProps> = ({
  courses,
  title,
  description
}) => {
  return (
    <div>
      {title && (
        <div className="text-center mb-12">
          <h2 className="text-3xl font-bold text-gray-900">{title}</h2>
          {description && (
            <p className="mt-4 text-xl text-gray-600">{description}</p>
          )}
        </div>
      )}
      
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        {courses.map((course) => (
          <CourseCard
            key={course.id}
            id={course.id}
            title={course.title}
            description={course.description}
            image={course.image}
            rating={course.rating}
            students={course.students}
            duration={course.duration}
            level={course.level}
            color={course.color}
          />
        ))}
      </div>
    </div>
  );
};

export default CourseList;

/== organisms/Footer.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Facebook, Twitter, Instagram, Linkedin, Youtube } from 'lucide-react';
import Logo from '../atoms/Logo';

const Footer: React.FC = () => {
  return (
    <footer className="bg-gray-900 text-white">
      <div className="max-w-7xl mx-auto py-12 px-4 sm:px-6 lg:px-8">
        <div className="grid grid-cols-1 md:grid-cols-4 gap-8">
          <div>
            <Logo />
            <p className="mt-4 text-sm text-gray-300">
              Learn to code with our easy-to-follow tutorials, examples, and references.
            </p>
            <div className="mt-6 flex space-x-4">
              <a href="#" className="text-gray-400 hover:text-primary-500">
                <Facebook className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-primary-500">
                <Twitter className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-primary-500">
                <Instagram className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-primary-500">
                <Linkedin className="h-5 w-5" />
              </a>
              <a href="#" className="text-gray-400 hover:text-primary-500">
                <Youtube className="h-5 w-5" />
              </a>
            </div>
          </div>
          
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">
              Tutorials
            </h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/tutorials/python" className="text-gray-300 hover:text-primary-500">
                  Python
                </Link>
              </li>
              {/* Add more tutorial links */}
            </ul>
          </div>
          
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">
              Resources
            </h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/compiler" className="text-gray-300 hover:text-primary-500">
                  Online Compiler
                </Link>
              </li>
              {/* Add more resource links */}
            </ul>
          </div>
          
          <div>
            <h3 className="text-sm font-semibold text-gray-200 tracking-wider uppercase">
              Company
            </h3>
            <ul className="mt-4 space-y-2">
              <li>
                <Link to="/about" className="text-gray-300 hover:text-primary-500">
                  About Us
                </Link>
              </li>
              {/* Add more company links */}
            </ul>
          </div>
        </div>
        
        <div className="mt-12 border-t border-gray-700 pt-8">
          <p className="text-sm text-gray-400">
            © {new Date().getFullYear()} Coder Growth. All rights reserved.
          </p>
        </div>
      </div>
    </footer>
  );
};

export default Footer;

/== organisms/GettingStarted.tsx
import React from 'react';
import Card from '../atoms/Card';
import Text from '../atoms/Text';
import Heading from '../atoms/Heading';
import Button from '../atoms/Button';

interface GettingStartedProps {
  steps: string[];
  firstTutorialPath: string;
}

const GettingStarted: React.FC<GettingStartedProps> = ({
  steps,
  firstTutorialPath
}) => {
  return (
    <Card className="p-6">
      <Heading level={2} className="mb-4">Getting Started</Heading>
      <div className="prose max-w-none">
        <Text variant="body" color="muted" className="mb-4">
          Ready to start learning? Here's what you need to do:
        </Text>
        <ol className="space-y-2 mb-6">
          {steps.map((step, index) => (
            <li key={index} className="flex items-start">
              <span className="text-blue-600 font-medium mr-2">{index + 1}.</span>
              <Text variant="body" color="muted">{step}</Text>
            </li>
          ))}
        </ol>
        <Button
          variant="primary"
          onClick={() => window.location.href = firstTutorialPath}
        >
          Start with the First Tutorial
        </Button>
      </div>
    </Card>
  );
};

export default GettingStarted;

/== organisms/Header.tsx
import React, { useState, useRef, useEffect } from 'react';
import { Link, useLocation, useNavigate } from 'react-router-dom';
import { Menu, ChevronDown, Search, Bell, LogOut, User, Settings, BookOpen, Star, Globe, Zap, Crown, Code, Map } from 'lucide-react';
import Logo from '../atoms/Logo';
import Button from '../atoms/Button';
import SearchBar from '../molecules/SearchBar';
import ThemeToggle from '../atoms/ThemeToggle';
import NotificationDropdown from '../molecules/NotificationDropdown';
import { useAuthStore } from '../../store/authStore';
import { useCourseStore } from '../../store/courseStore';
import { useNotificationStore } from '../../store/notificationStore';

const Header: React.FC = () => {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [searchQuery, setSearchQuery] = useState('');
  const [isUserMenuOpen, setIsUserMenuOpen] = useState(false);
  const [isNotificationsOpen, setIsNotificationsOpen] = useState(false);
  const notificationRef = useRef<HTMLDivElement>(null);
  const { isAuthenticated, user, logout } = useAuthStore();
  const { totalXp } = useCourseStore();
  const { unreadCount } = useNotificationStore();
  const location = useLocation();
  const navigate = useNavigate();

  useEffect(() => {
    const handleClickOutside = (event: MouseEvent) => {
      if (notificationRef.current && !notificationRef.current.contains(event.target as Node)) {
        setIsNotificationsOpen(false);
      }
    };

    document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, []);

  const handleDashboardClick = () => {
    if (user?.role === 'admin') {
      navigate('/cms');
    } else {
      navigate('/dashboard');
    }
  };

  const navItems = [
    {
      label: 'Learn',
      items: [
        { label: 'Python', href: '/tutorials/python' },
        { label: 'JavaScript', href: '/tutorials/javascript' },
        { label: 'Java', href: '/tutorials/java' },
        { label: 'Go', href: '/tutorials/go' },
        { label: 'DSA', href: '/tutorials/dsa' },
      ]
    },
    {
      label: 'Practice',
      items: [
        { label: 'Compiler', href: '/compiler' },
        { label: 'Challenges', href: '/challenges' },
        { label: 'Projects', href: '/projects' },
      ]
    },
    {
      label: 'Resources',
      items: [
        { label: 'Documentation', href: '/docs' },
        { label: 'Examples', href: '/examples' },
        { label: 'Blog', href: '/blog' },
      ]
    }
  ];

  const userMenuItems = [
    { label: 'Profile', icon: User, href: '/profile' },
    { label: 'My Courses', icon: BookOpen, href: '/my-learning' },
    { label: 'Settings', icon: Settings, href: '/settings' },
    { label: 'Sign Out', icon: LogOut, onClick: logout }
  ];

  return (
    <header className="bg-white dark:bg-gray-900 border-b border-gray-200 dark:border-gray-800 sticky top-0 z-50">
      <div className="max-w-7xl mx-auto">
        <div className="flex items-center justify-between h-16 px-4 sm:px-6 lg:px-8">
          {/* Logo */}
          <div className="flex items-center">
            <Logo />
          </div>

          {/* Desktop Navigation */}
          <nav className="hidden md:flex items-center space-x-8">
            {navItems.map((item) => (
              <div key={item.label} className="relative group">
                <button className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400">
                  {item.label}
                  <ChevronDown className="ml-1 h-4 w-4" />
                </button>
                <div className="absolute left-0 mt-2 w-48 rounded-md shadow-lg bg-white dark:bg-gray-800 ring-1 ring-black ring-opacity-5 opacity-0 invisible group-hover:opacity-100 group-hover:visible transition-all duration-200">
                  <div className="py-1" role="menu">
                    {item.items.map((subItem) => (
                      <Link
                        key={subItem.href}
                        to={subItem.href}
                        className="block px-4 py-2 text-sm text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700"
                        role="menuitem"
                      >
                        {subItem.label}
                      </Link>
                    ))}
                  </div>
                </div>
              </div>
            ))}
            <Link
              to="/roadmap"
              className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400"
            >
              <Map className="h-4 w-4 mr-1" />
              Roadmaps
            </Link>
            <Link
              to="/pricing"
              className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400"
            >
              Pricing
            </Link>
          </nav>

          {/* Right Section */}
          <div className="hidden md:flex items-center space-x-4">
            <ThemeToggle />

            {isAuthenticated ? (
              <div className="flex items-center space-x-4">
                <div className="relative" ref={notificationRef}>
                  <button
                    onClick={() => setIsNotificationsOpen(!isNotificationsOpen)}
                    className="relative text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 p-1 rounded-full hover:bg-gray-100 dark:hover:bg-gray-800"
                  >
                    <Bell className="h-6 w-6" />
                    {unreadCount > 0 && (
                      <span className="absolute top-0 right-0 block h-4 w-4 rounded-full bg-red-500 border-2 border-white dark:border-gray-900 text-xs text-white flex items-center justify-center">
                        {unreadCount}
                      </span>
                    )}
                  </button>
                  <NotificationDropdown
                    isOpen={isNotificationsOpen}
                    onClose={() => setIsNotificationsOpen(false)}
                  />
                </div>
                <button 
                  onClick={handleDashboardClick}
                  className="text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400 font-medium"
                >
                  Dashboard
                </button>
                <div className="relative">
                  <button
                    onClick={() => setIsUserMenuOpen(!isUserMenuOpen)}
                    className="flex items-center space-x-2 text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400"
                  >
                    <div className="w-8 h-8 bg-primary-500 rounded-full flex items-center justify-center text-white">
                      {user?.name.charAt(0)}
                    </div>
                    <ChevronDown className="h-4 w-4" />
                  </button>

                  {isUserMenuOpen && (
                    <div className="absolute right-0 mt-2 w-48 bg-white dark:bg-gray-800 rounded-lg shadow-lg py-1 ring-1 ring-black ring-opacity-5">
                      <div className="px-4 py-2 border-b border-gray-200 dark:border-gray-700">
                        <div className="font-medium text-gray-900 dark:text-white">{user?.name}</div>
                        <div className="flex items-center text-sm text-yellow-500">
                          <Star className="h-4 w-4 mr-1 fill-current" />
                          {totalXp} XP
                        </div>
                      </div>
                      {userMenuItems.map((item) => (
                        item.href ? (
                          <Link
                            key={item.label}
                            to={item.href}
                            className="flex items-center px-4 py-2 text-sm text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700"
                            onClick={() => setIsUserMenuOpen(false)}
                          >
                            <item.icon className="h-4 w-4 mr-2" />
                            {item.label}
                          </Link>
                        ) : (
                          <button
                            key={item.label}
                            onClick={() => {
                              item.onClick?.();
                              setIsUserMenuOpen(false);
                            }}
                            className="flex items-center w-full px-4 py-2 text-sm text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700"
                          >
                            <item.icon className="h-4 w-4 mr-2" />
                            {item.label}
                          </button>
                        )
                      ))}
                    </div>
                  )}
                </div>
              </div>
            ) : (
              <div className="flex items-center space-x-4">
                <Link 
                  to="/login"
                  state={{ from: location }}
                  className="bg-brand-green hover:bg-brand-green/90 text-white px-4 py-2 rounded-lg"
                >
                  Sign In
                </Link>
              </div>
            )}
          </div>

          {/* Mobile Menu Button */}
          <div className="flex md:hidden">
            <ThemeToggle />
            <Button
              variant="outline"
              icon={Menu}
              onClick={() => setIsMenuOpen(!isMenuOpen)}
              className="ml-2"
            />
          </div>
        </div>
      </div>

      {/* Mobile Menu */}
      {isMenuOpen && (
        <div className="md:hidden">
          <div className="px-2 pt-2 pb-3 space-y-1">
            {navItems.map((item) => (
              <div key={item.label}>
                <div className="px-3 py-2 text-base font-medium text-gray-700 dark:text-gray-300">
                  {item.label}
                </div>
                {item.items.map((subItem) => (
                  <Link
                    key={subItem.href}
                    to={subItem.href}
                    className="block px-3 py-2 text-base font-medium text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 hover:bg-gray-50 dark:hover:bg-gray-800"
                  >
                    {subItem.label}
                  </Link>
                ))}
              </div>
            ))}
            <Link
              to="/roadmap"
              className="block px-3 py-2 text-base font-medium text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 hover:bg-gray-50 dark:hover:bg-gray-800"
            >
              <div className="flex items-center">
                <Map className="h-4 w-4 mr-2" />
                Roadmaps
              </div>
            </Link>
            <Link
              to="/pricing"
              className="block px-3 py-2 text-base font-medium text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 hover:bg-gray-50 dark:hover:bg-gray-800"
            >
              Pricing
            </Link>
          </div>
          <div className="pt-4 pb-3 border-t border-gray-200 dark:border-gray-700">
            <div className="px-2 space-y-1">
              {!isAuthenticated ? (
                <Link
                  to="/login"
                  state={{ from: location }}
                  className="block w-full text-left px-3 py-2 rounded-md text-base font-medium text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-800"
                >
                  Sign In
                </Link>
              ) : (
                <>
                  <div className="px-3 py-2 border-b border-gray-200 dark:border-gray-700 mb-2">
                    <div className="font-medium text-gray-900 dark:text-white">{user?.name}</div>
                    <div className="flex items-center text-sm text-yellow-500">
                      <Star className="h-4 w-4 mr-1 fill-current" />
                      {totalXp} XP
                    </div>
                  </div>
                  {userMenuItems.map((item) => (
                    item.href ? (
                      <Link
                        key={item.label}
                        to={item.href}
                        className="flex items-center px-3 py-2 text-base font-medium text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-800"
                      >
                        <item.icon className="h-5 w-5 mr-2" />
                        {item.label}
                      </Link>
                    ) : (
                      <button
                        key={item.label}
                        onClick={item.onClick}
                        className="flex items-center w-full px-3 py-2 text-base font-medium text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-800"
                      >
                        <item.icon className="h-5 w-5 mr-2" />
                        {item.label}
                      </button>
                    )
                  ))}
                </>
              )}
            </div>
          </div>
        </div>
      )}
    </header>
  );
};

export default Header;

/== organisms/HeroSection.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { ArrowRight, Code } from 'lucide-react';
import Button from '../atoms/Button';

interface HeroSectionProps {
  title: string;
  description: string;
  image?: string;
  primaryAction?: {
    text: string;
    link: string;
  };
  secondaryAction?: {
    text: string;
    link: string;
  };
  background?: string;
}

const HeroSection: React.FC<HeroSectionProps> = ({
  title,
  description,
  image,
  primaryAction,
  secondaryAction,
  background = 'bg-gradient-to-r from-gray-900 to-gray-800'
}) => {
  return (
    <section className={`${background} relative overflow-hidden`}>
      {/* Background Pattern */}
      <div className="absolute inset-0 bg-grid-pattern opacity-10"></div>
      
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24 relative">
        <div className="grid grid-cols-1 md:grid-cols-2 gap-12 items-center">
          <div className="text-center md:text-left">
            <div className="inline-flex items-center bg-brand-green bg-opacity-10 rounded-full px-4 py-2 mb-6">
              <Code className="h-5 w-5 text-brand-green mr-2" />
              <span className="text-sm font-medium text-brand-green">Learn to code for free</span>
            </div>
            
            <h1 className="text-4xl md:text-5xl lg:text-6xl font-bold text-white mb-6 leading-tight">
              {title}
            </h1>
            
            <p className="text-xl text-gray-300 mb-8 leading-relaxed">
              {description}
            </p>
            
            <div className="flex flex-col sm:flex-row justify-center md:justify-start space-y-4 sm:space-y-0 sm:space-x-4">
              {primaryAction && (
                <Button
                  href={primaryAction.link}
                  variant="primary"
                  className="text-lg px-8 py-4 bg-brand-green hover:bg-brand-green/90"
                >
                  {primaryAction.text}
                  <ArrowRight className="ml-2 h-5 w-5" />
                </Button>
              )}
              
              {secondaryAction && (
                <Button
                  href={secondaryAction.link}
                  variant="outline"
                  className="text-lg px-8 py-4 text-white border-white hover:bg-white/10"
                >
                  {secondaryAction.text}
                </Button>
              )}
            </div>
          </div>
          
          {image && (
            <div className="hidden md:block relative">
              <div className="absolute -inset-4 bg-brand-green/20 rounded-lg blur-xl"></div>
              <img
                src={image}
                alt="Hero"
                className="relative rounded-lg shadow-2xl transform hover:scale-105 transition-transform duration-300"
              />
            </div>
          )}
        </div>
      </div>
    </section>
  );
};

export default HeroSection;

/== organisms/IntegrationsDashboard.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { Server, CreditCard, Shield, Globe, Webhook, AlertCircle, CheckCircle } from 'lucide-react';
import Card from '../atoms/Card';
import Heading from '../atoms/Heading';
import Text from '../atoms/Text';
import Button from '../atoms/Button';

interface IntegrationStatusProps {
  title: string;
  status: 'active' | 'inactive' | 'error';
  count: number;
  icon: React.ReactNode;
  path: string;
}

const IntegrationStatus: React.FC<IntegrationStatusProps> = ({ title, status, count, icon, path }) => {
  const getStatusColor = () => {
    switch (status) {
      case 'active':
        return 'text-green-500';
      case 'inactive':
        return 'text-gray-400';
      case 'error':
        return 'text-red-500';
      default:
        return 'text-gray-400';
    }
  };

  const getStatusIcon = () => {
    switch (status) {
      case 'active':
        return <CheckCircle className={`h-5 w-5 ${getStatusColor()}`} />;
      case 'inactive':
        return <AlertCircle className={`h-5 w-5 ${getStatusColor()}`} />;
      case 'error':
        return <AlertCircle className={`h-5 w-5 ${getStatusColor()}`} />;
      default:
        return null;
    }
  };

  return (
    <Card className="p-4">
      <div className="flex items-center justify-between mb-2">
        <div className="flex items-center">
          <div className="p-2 rounded-full bg-brand-green/10 text-brand-green mr-3">
            {icon}
          </div>
          <Heading level="h3" className="text-lg font-medium text-gray-600 dark:text-white">{title}</Heading>
        </div>
        <div className="flex items-center">
          {getStatusIcon()}
          <span className={`ml-2 text-sm font-medium text-gray-600 dark:text-white ${getStatusColor()}`}>
            {status === 'active' ? 'Aktif' : status === 'inactive' ? 'Tidak Aktif' : 'Error'}
          </span>
        </div>
      </div>
      <div className="flex items-center justify-between">
        <Text className="text-2xl font-bold text-gray-600 dark:text-white">{count}</Text>
        <Link to={path}>
          <Button variant="secondary" size="sm" className="text-sm">
            Kelola
          </Button>
        </Link>
      </div>
    </Card>
  );
};

interface IntegrationsDashboardProps {
  apiKeysCount?: number;
  paymentGatewaysCount?: number;
  ssoProvidersCount?: number;
  thirdPartyCount?: number;
  webhooksCount?: number;
}

const IntegrationsDashboard: React.FC<IntegrationsDashboardProps> = ({
  apiKeysCount = 0,
  paymentGatewaysCount = 0,
  ssoProvidersCount = 0,
  thirdPartyCount = 0,
  webhooksCount = 0
}) => {
  const integrationStats = [
    {
      title: 'API Keys',
      status: apiKeysCount > 0 ? 'active' : 'inactive' as 'active' | 'inactive' | 'error',
      count: apiKeysCount,
      icon: <Server className="h-5 w-5" />,
      path: '/cms/integrations/api-management'
    },
    {
      title: 'Gateway Pembayaran',
      status: paymentGatewaysCount > 0 ? 'active' : 'inactive' as 'active' | 'inactive' | 'error',
      count: paymentGatewaysCount,
      icon: <CreditCard className="h-5 w-5" />,
      path: '/cms/integrations/payment-gateways'
    },
    {
      title: 'SSO Providers',
      status: ssoProvidersCount > 0 ? 'active' : 'inactive' as 'active' | 'inactive' | 'error',
      count: ssoProvidersCount,
      icon: <Shield className="h-5 w-5" />,
      path: '/cms/integrations/sso'
    },
    {
      title: 'Integrasi Pihak Ketiga',
      status: thirdPartyCount > 0 ? 'active' : 'inactive' as 'active' | 'inactive' | 'error',
      count: thirdPartyCount,
      icon: <Globe className="h-5 w-5" />,
      path: '/cms/integrations/third-party'
    },
    {
      title: 'Webhooks',
      status: webhooksCount > 0 ? 'active' : 'inactive' as 'active' | 'inactive' | 'error',
      count: webhooksCount,
      icon: <Webhook className="h-5 w-5" />,
      path: '/cms/integrations/webhooks'
    }
  ];

  return (
    <div>
      <div className="mb-6">
        <Heading level="h2" className="text-xl font-bold mb-2">Status Integrasi</Heading>
        <Text className="text-gray-600 dark:text-white">
          Ringkasan status semua integrasi sistem
        </Text>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 text-gray-600 dark:text-white">
        {integrationStats.map((stat, index) => (
          <IntegrationStatus
            key={index}
            title={stat.title}
            status={stat.status}
            count={stat.count}
            icon={stat.icon}
            path={stat.path}
          />
        ))}
      </div>
    </div>
  );
};

export default IntegrationsDashboard;

/== organisms/LessonContent.tsx
import React from 'react';
import { LearningStep } from '../../store/learningStore';
import Button from '../atoms/Button';
import { CheckCircle } from 'lucide-react';

interface LessonContentProps {
  step: LearningStep;
  selectedOption: string | null;
  isAnswerCorrect: boolean | null;
  testPassed: boolean;
  onOptionSelect: (optionId: string) => void;
}

const LessonContent: React.FC<LessonContentProps> = ({
  step,
  selectedOption,
  isAnswerCorrect,
  testPassed,
  onOptionSelect
}) => {
  const renderQuizContent = () => (
    <div>
      <div className="prose max-w-none" dangerouslySetInnerHTML={{ __html: step.content }} />
      
      <div className="mt-8 space-y-4">
        {step.options?.map((option) => (
          <div 
            key={option.id}
            className={`p-4 rounded-lg border ${
              selectedOption === option.id
                ? option.isCorrect
                  ? 'bg-green-100 border-green-500'
                  : 'bg-red-100 border-red-500'
                : 'bg-white border-gray-300 hover:border-blue-500'
            } cursor-pointer transition-colors`}
            onClick={() => onOptionSelect(option.id)}
          >
            <div className="flex items-center">
              <div className="mr-4 font-medium">{option.id.toUpperCase()}:</div>
              <div className="font-mono">{option.text}</div>
            </div>
          </div>
        ))}
      </div>
      
      {isAnswerCorrect !== null && (
        <div className={`mt-6 p-4 rounded-lg ${isAnswerCorrect ? 'bg-green-100' : 'bg-red-100'}`}>
          <div className="flex items-center">
            {isAnswerCorrect ? (
              <CheckCircle className="h-5 w-5 text-green-600 mr-2" />
            ) : (
              <div className="h-5 w-5 text-red-600 mr-2">✗</div>
            )}
            <div className="font-medium">
              {isAnswerCorrect ? 'Correct Answer!' : 'Incorrect Answer!'}
            </div>
          </div>
        </div>
      )}
    </div>
  );

  const renderPracticeContent = () => (
    <div>
      <div className="prose max-w-none" dangerouslySetInnerHTML={{ __html: step.content }} />
      
      {testPassed && (
        <div className="mt-6 p-4 bg-green-100 rounded-lg">
          <div className="flex items-center">
            <CheckCircle className="h-5 w-5 text-green-600 mr-2" />
            <div className="font-medium">Great job! You've completed this practice exercise!</div>
          </div>
        </div>
      )}
    </div>
  );

  const renderLessonContent = () => (
    <div className="prose max-w-none" dangerouslySetInnerHTML={{ __html: step.content }} />
  );

  switch (step.type) {
    case 'quiz':
      return renderQuizContent();
    case 'practice':
      return renderPracticeContent();
    default:
      return renderLessonContent();
  }
};

export default LessonContent;

/== organisms/PricingSection.tsx
import React from 'react';
import { Info } from 'lucide-react';
import PricingToggle from '../atoms/PricingToggle';
import PricingCard from '../molecules/PricingCard';

interface PricingSectionProps {
  billingCycle: 'yearly' | 'monthly';
  onBillingCycleChange: (cycle: 'yearly' | 'monthly') => void;
  onSubscribe: (plan: string) => void;
}

const PricingSection: React.FC<PricingSectionProps> = ({
  billingCycle,
  onBillingCycleChange,
  onSubscribe
}) => {
  const plans = {
    free: {
      name: 'Free',
      description: 'Remains free forever',
      price: 0,
      features: [
        { name: 'Early chapters', included: true },
        { name: 'Limited challenges', included: true },
        { name: 'Unlimited AI guidance', included: false },
        { name: 'Practice projects', included: false },
        { name: 'Professional certifications', included: false },
        { name: 'Unlimited coding playground', included: false }
      ]
    },
    premium: {
      name: 'Premium',
      description: 'Access to everything',
      monthlyPrice: 48194,
      yearlyPrice: 578317,
      features: [
        { name: 'Full course catalog', included: true },
        { name: 'Coding challenges', included: true },
        { name: 'Unlimited AI guidance', included: true },
        { name: 'Practice projects', included: true },
        { name: 'Interview preparation courses', included: true },
        { name: 'Professional certifications', included: true },
        { name: 'Unlimited coding playground', included: true }
      ]
    },
    lifetime: {
      name: 'Lifetime',
      description: 'Access to PRO forever',
      price: 1923097,
      features: [
        { name: 'Everything in Premium plus:', included: true },
        { name: 'Lifetime access', included: true },
        { name: 'No recurring payments', included: true }
      ]
    }
  };

  return (
    <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
      <div className="text-center">
        <h1 className="text-4xl font-bold text-gray-900 mb-4">
          Choose the best plan for you.
        </h1>
        
        <div className="flex justify-center items-center space-x-4 mb-8">
          <PricingToggle
            value={billingCycle}
            onChange={onBillingCycleChange}
          />
          {billingCycle === 'yearly' && (
            <span className="inline-flex items-center px-3 py-1 rounded-full text-sm font-medium bg-green-100 text-green-800">
              Save 66%
            </span>
          )}
        </div>

        <div className="flex items-center justify-center text-sm text-gray-600 mb-12">
          <Info className="h-4 w-4 mr-2" />
          Price discounted based on your region
        </div>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
        <PricingCard
          name={plans.free.name}
          description={plans.free.description}
          price={plans.free.price}
          period="month"
          features={plans.free.features}
          onSubscribe={() => onSubscribe('free')}
          buttonText="Start for free"
          buttonVariant="outline"
        />

        <PricingCard
          name={plans.premium.name}
          description={plans.premium.description}
          price={billingCycle === 'yearly' ? plans.premium.yearlyPrice : plans.premium.monthlyPrice}
          period={billingCycle === 'yearly' ? 'year' : 'month'}
          features={plans.premium.features}
          popular={true}
          onSubscribe={() => onSubscribe('premium')}
          buttonText="Get premium"
        />

        <PricingCard
          name={plans.lifetime.name}
          description={plans.lifetime.description}
          price={plans.lifetime.price}
          features={plans.lifetime.features}
          onSubscribe={() => onSubscribe('lifetime')}
          buttonText="Get lifetime access"
          buttonVariant="secondary"
        />
      </div>
    </div>
  );
};

export default PricingSection;

/== organisms/RelatedCourses.tsx
import React from 'react';
import Card from '../atoms/Card';
import Button from '../atoms/Button';

interface RelatedCoursesProps {
  courses: {
    title: string;
    description: string;
    image: string;
    link: string;
  }[];
}

const RelatedCourses: React.FC<RelatedCoursesProps> = ({ courses }) => {
  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
      {courses.map((course, index) => (
        <Card key={index} className="overflow-hidden">
          <img
            src={course.image}
            alt={course.title}
            className="w-full h-48 object-cover"
          />
          <div className="p-6">
            <h3 className="text-xl font-bold text-gray-900 mb-2">{course.title}</h3>
            <p className="text-gray-600 mb-4">{course.description}</p>
            <Button
              variant="primary"
              onClick={() => window.location.href = course.link}
            >
              View Course
            </Button>
          </div>
        </Card>
      ))}
    </div>
  );
};

export default RelatedCourses;

/== organisms/RevenueAnalytics.tsx
import React, { useState } from "react";
import Button from "../atoms/Button";
import Card from "../atoms/Card";
import Heading from "../atoms/Heading";
import Input from "../atoms/Input";
import Select from "../atoms/Select";
import { 
  KpiMetric,
  DemographicItem,
  CohortData,
  DiscountData,
  MarketingChannel,
  SelectOption,
  ChartDataItem,
  TableDataItem
} from "../../types";

const RevenueAnalytics: React.FC = () => {
    // State declarations
    const [activeTab, setActiveTab] = useState<string>('overview');
    const [period, setPeriod] = useState<string>('monthly');
    const [dataView, setDataView] = useState<string>('chart');
    const [selectedMetric, setSelectedMetric] = useState<string>('revenue');
    const [showAnomalyAlert, setShowAnomalyAlert] = useState<boolean>(true);
    const [startDate, setStartDate] = useState<string>("2023-01-01");
    const [endDate, setEndDate] = useState<string>("2023-12-31");
    
    // Pilihan period filter
    const periodOptions: SelectOption[] = [
      { value: 'daily', label: 'Harian' },
      { value: 'weekly', label: 'Mingguan' },
      { value: 'monthly', label: 'Bulanan' },
      { value: 'quarterly', label: 'Kuartalan' },
      { value: 'yearly', label: 'Tahunan' }
    ];
    
    // Data untuk KPI metrics
    const kpiMetrics: KpiMetric[] = [
      { 
        name: 'MRR', 
        value: 'Rp 42.500.000', 
        trend: '+8.5%', 
        description: 'Monthly Recurring Revenue' 
      },
      { 
        name: 'LTV', 
        value: 'Rp 1.250.000', 
        trend: '+12.2%', 
        description: 'Lifetime Value' 
      },
      { 
        name: 'CAC', 
        value: 'Rp 320.000', 
        trend: '-5.1%', 
        description: 'Customer Acquisition Cost' 
      },
      { 
        name: 'ARPU', 
        value: 'Rp 175.000', 
        trend: '+3.2%', 
        description: 'Average Revenue Per User' 
      }
    ];
    
    // Data untuk chart
    const chartData: ChartDataItem[] = [
      { month: "Jan", value: 65 },
      { month: "Feb", value: 59 },
      { month: "Mar", value: 80 },
      { month: "Apr", value: 81 },
      { month: "Mei", value: 56 },
      { month: "Jun", value: 55 },
      { month: "Jul", value: 40 },
      { month: "Ags", value: 70 },
      { month: "Sep", value: 90 },
      { month: "Okt", value: 75 },
      { month: "Nov", value: 85 },
      { month: "Des", value: 100 },
    ];
    
    // Data untuk table view
    const tableData: TableDataItem[] = [
      { month: "Jan", revenue: 9750000, growth: "+5.2%", newCustomers: 85 },
      { month: "Feb", revenue: 8850000, growth: "-9.2%", newCustomers: 72 },
      { month: "Mar", revenue: 12000000, growth: "+35.6%", newCustomers: 124 },
      { month: "Apr", revenue: 12150000, growth: "+1.3%", newCustomers: 118 },
      { month: "Mei", revenue: 8400000, growth: "-30.9%", newCustomers: 95 },
      { month: "Jun", revenue: 8250000, growth: "-1.8%", newCustomers: 89 },
      { month: "Jul", revenue: 6000000, growth: "-27.3%", newCustomers: 68 },
      { month: "Ags", revenue: 10500000, growth: "+75.0%", newCustomers: 112 },
      { month: "Sep", revenue: 13500000, growth: "+28.6%", newCustomers: 139 },
      { month: "Okt", revenue: 11250000, growth: "-16.7%", newCustomers: 108 },
      { month: "Nov", revenue: 12750000, growth: "+13.3%", newCustomers: 125 },
      { month: "Des", revenue: 15000000, growth: "+17.6%", newCustomers: 145 },
    ];
    
    // Data untuk demografi
    const demographicData: DemographicItem[] = [
      { location: 'Jakarta', percentage: 35, revenue: 44012500 },
      { location: 'Bandung', percentage: 15, revenue: 18862500 },
      { location: 'Surabaya', percentage: 12, revenue: 15090000 },
      { location: 'Yogyakarta', percentage: 10, revenue: 12575000 },
      { location: 'Medan', percentage: 8, revenue: 10060000 },
      { location: 'Other', percentage: 20, revenue: 25150000 }
    ];
    
    // Data untuk analisis kohort
    const cohortData: CohortData[] = [
      { month: 'Jan 2023', m1: '100%', m2: '85%', m3: '72%', m4: '68%', m5: '65%', m6: '62%' },
      { month: 'Feb 2023', m1: '100%', m2: '82%', m3: '75%', m4: '70%', m5: '68%', m6: '65%' },
      { month: 'Mar 2023', m1: '100%', m2: '88%', m3: '80%', m4: '76%', m5: '72%', m6: '70%' },
      { month: 'Apr 2023', m1: '100%', m2: '85%', m3: '78%', m4: '73%', m5: '70%', m6: '-' },
      { month: 'May 2023', m1: '100%', m2: '87%', m3: '80%', m4: '75%', m5: '-', m6: '-' },
      { month: 'Jun 2023', m1: '100%', m2: '84%', m3: '75%', m4: '-', m5: '-', m6: '-' },
    ];
    
    // Data untuk efektivitas diskon
    const discountData: DiscountData[] = [
      { name: 'WELCOME25', redemptions: 450, revenue: 15750000, avgOrder: 350000 },
      { name: 'BUNDLE10', redemptions: 320, revenue: 25600000, avgOrder: 800000 },
      { name: 'FLASH50', redemptions: 280, revenue: 7000000, avgOrder: 250000 },
      { name: 'SUMMER20', redemptions: 210, revenue: 10500000, avgOrder: 500000 },
    ];
    
    // Data untuk channel marketing
    const marketingData: MarketingChannel[] = [
      { channel: 'Organic Search', visits: 15200, conversions: 380, convRate: '2.5%', revenue: 38000000, roi: '650%' },
      { channel: 'Paid Search', visits: 8500, conversions: 255, convRate: '3.0%', revenue: 25500000, roi: '320%' },
      { channel: 'Social Media', visits: 12600, conversions: 189, convRate: '1.5%', revenue: 18900000, roi: '210%' },
      { channel: 'Email', visits: 7800, conversions: 312, convRate: '4.0%', revenue: 31200000, roi: '780%' },
      { channel: 'Referral', visits: 4200, conversions: 168, convRate: '4.0%', revenue: 16800000, roi: '540%' },
    ];
    
    // Helper functions
    const formatCurrency = (value: number): string => {
      return `Rp ${value.toLocaleString('id-ID')}`;
    };
    
    // Event handlers
    const handleTabChange = (tab: string): void => {
      setActiveTab(tab);
    };
    
    const toggleDataView = (): void => {
      setDataView(dataView === 'chart' ? 'table' : 'chart');
    };
    return (
        <div className="p-4 md:p-6 lg:p-8 dark:bg-gray-900">
          {/* Header dan tools */}
          <div className="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
            <Heading level={2} className="text-gray-900 dark:text-white mb-4 md:mb-0">Analitik Pendapatan</Heading>
            
            <div className="flex items-center space-x-3">
              <Button 
                variant="outline" 
                className="text-sm py-1 px-3 dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700"
                onClick={toggleDataView}
              >
                <span className="flex items-center">
                  {dataView === 'chart' ? (
                    <>
                      <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 6h16M4 10h16M4 14h16M4 18h16" />
                      </svg>
                      Tabel
                    </>
                  ) : (
                    <>
                      <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
                      </svg>
                      Grafik
                    </>
                  )}
                </span>
              </Button>
              
              <div className="dropdown relative">
                <Button 
                  variant="outline" 
                  className="text-sm py-1 px-3 dark:border-gray-600 dark:text-gray-300 dark:hover:bg-gray-700"
                >
                  <span className="flex items-center">
                    <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                    </svg>
                    Ekspor
                    <svg className="w-4 h-4 ml-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 9l-7 7-7-7" />
                    </svg>
                  </span>
                </Button>
                {/* Dropdown menu untuk ekspor */}
                <div className="hidden absolute right-0 mt-2 w-48 bg-white dark:bg-gray-800 rounded-md shadow-lg z-10 border border-gray-200 dark:border-gray-700">
                  <ul className="py-1">
                    <li>
                      <button className="block w-full text-left px-4 py-2 text-sm text-gray-700 dark:text-gray-200 hover:bg-gray-100 dark:hover:bg-gray-700">
                        Ekspor CSV
                      </button>
                    </li>
                    <li>
                      <button className="block w-full text-left px-4 py-2 text-sm text-gray-700 dark:text-gray-200 hover:bg-gray-100 dark:hover:bg-gray-700">
                        Ekspor Excel
                      </button>
                    </li>
                    <li>
                      <button className="block w-full text-left px-4 py-2 text-sm text-gray-700 dark:text-gray-200 hover:bg-gray-100 dark:hover:bg-gray-700">
                        Ekspor PDF
                      </button>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
          
          {/* Anomaly Alert */}
          {showAnomalyAlert && (
            <div className="mb-6 bg-yellow-50 dark:bg-yellow-900/20 border-l-4 border-yellow-400 dark:border-yellow-600 p-4 rounded">
              <div className="flex items-start">
                <div className="flex-shrink-0">
                  <svg className="h-5 w-5 text-yellow-400 dark:text-yellow-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                  </svg>
                </div>
                <div className="ml-3">
                  <h3 className="text-sm font-medium text-yellow-800 dark:text-yellow-200">Deteksi Anomali</h3>
                  <div className="mt-1 text-sm text-yellow-700 dark:text-yellow-300">
                    Terjadi peningkatan pendapatan 75% pada Agustus 2023 dibandingkan bulan sebelumnya. Ini mungkin terkait dengan peluncuran bundle Web Development.
                  </div>
                  <div className="mt-2">
                    <Button 
                      size="sm" 
                      variant="outline"
                      className="text-xs py-1 px-2 dark:border-yellow-600 dark:text-yellow-300 dark:hover:bg-yellow-900/40"
                      onClick={() => setShowAnomalyAlert(false)}
                    >
                      Dismiss
                    </Button>
                  </div>
                </div>
              </div>
            </div>
          )}
          
          {/* Tab Navigation */}
          <div className="border-b border-gray-200 dark:border-gray-700 mb-6 overflow-x-auto">
            <ul className="flex flex-nowrap -mb-px whitespace-nowrap">
              {[
                { id: 'overview', label: 'Ikhtisar' },
                { id: 'metrics', label: 'Metrik Kunci' },
                { id: 'product', label: 'Analisis Produk' },
                { id: 'demographic', label: 'Demografis' },
                { id: 'discount', label: 'Efektivitas Diskon' },
                { id: 'marketing', label: 'ROI Pemasaran' },
                { id: 'advanced', label: 'Pelaporan Lanjutan' }
              ].map((tab) => (
                <li key={tab.id} className="mr-2">
                  <button
                    className={`inline-block py-3 px-4 text-sm font-medium border-b-2 rounded-t-lg ${
                      activeTab === tab.id
                        ? 'border-indigo-600 text-indigo-600 dark:border-indigo-500 dark:text-indigo-500'
                        : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 dark:text-gray-400 dark:hover:text-gray-300'
                    }`}
                    onClick={() => handleTabChange(tab.id)}
                  >
                    {tab.label}
                  </button>
                </li>
              ))}
            </ul>
          </div>
          
          {/* Filter Controls */}
          <div className="flex flex-col md:flex-row md:items-end gap-3 md:gap-4 my-6 bg-gray-50 dark:bg-gray-800 p-4 rounded-lg">
            <div className="flex-1 grid grid-cols-1 md:grid-cols-3 gap-4">
              <Input 
                type="date" 
                label="Tanggal Mulai" 
                value={startDate}
                onChange={(e) => setStartDate(e.target.value)}
                className="w-full dark:bg-gray-700 dark:text-gray-200 dark:border-gray-600"
              />
              <Input 
                type="date" 
                label="Tanggal Akhir" 
                value={endDate}
                onChange={(e) => setEndDate(e.target.value)}
                className="w-full dark:bg-gray-700 dark:text-gray-200 dark:border-gray-600"
              />
              <Select
                label="Periode"
                value={period}
                onChange={(e) => setPeriod(e.target.value)}
                className="w-full dark:bg-gray-700 dark:text-gray-200 dark:border-gray-600"
                options={periodOptions}
              />
            </div>
            <Button className="mt-2 md:mt-0 w-full md:w-auto dark:bg-indigo-600 dark:hover:bg-indigo-700">
              Terapkan Filter
            </Button>
          </div>
           {/* Overview Tab Content */}
      {activeTab === 'overview' && (
        <>
          {/* Stats cards */}
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Total Pendapatan</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">Rp 125.750.000</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +12.5% dari periode sebelumnya
              </div>
            </Card>
            
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Langganan Aktif</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">1,250</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +8.2% dari periode sebelumnya
              </div>
            </Card>
            
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Pendapatan per Kursus</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">Rp 2.450.000</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +5.7% dari periode sebelumnya
              </div>
            </Card>
          </div>

          {/* Revenue chart */}
          <Card className="mb-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
            <div className="p-4 md:p-6">
              <div className="flex justify-between items-center mb-6">
                <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-0">
                  Tren Pendapatan {period === 'monthly' ? 'Bulanan' : period === 'daily' ? 'Harian' : period === 'weekly' ? 'Mingguan' : period === 'quarterly' ? 'Kuartalan' : 'Tahunan'}
                </Heading>
                <div className="flex items-center space-x-2">
                  <select 
                    value={selectedMetric}
                    onChange={(e) => setSelectedMetric(e.target.value)}
                    className="text-sm border border-gray-300 rounded-md p-1 dark:bg-gray-700 dark:border-gray-600 dark:text-gray-200"
                  >
                    <option value="revenue">Pendapatan</option>
                    <option value="customers">Pelanggan</option>
                    <option value="orders">Pesanan</option>
                  </select>
                  <div className="text-sm text-gray-500 dark:text-gray-400">2023</div>
                </div>
              </div>
              
              {dataView === 'chart' ? (
                <div className="relative h-64 md:h-80">
                  {/* Y-axis labels */}
                  <div className="absolute top-0 left-0 h-full flex flex-col justify-between text-xs text-gray-500 dark:text-gray-400 py-2">
                    <div>Rp 15M</div>
                    <div>Rp 10M</div>
                    <div>Rp 5M</div>
                    <div>Rp 0</div>
                  </div>
                  
                  {/* Chart */}
                  <div className="ml-12 h-full flex items-end space-x-1 md:space-x-2">
                    {chartData.map((item) => (
                      <div key={item.month} className="flex-1 flex flex-col items-center">
                        <div className="group relative w-full">
                          <div 
                            className="w-4/5 mx-auto bg-emerald-500 dark:bg-emerald-600 hover:bg-emerald-600 dark:hover:bg-emerald-500 rounded-t transition-all cursor-pointer"
                            style={{ height: `${item.value * 2}px` }}
                          >
                            {/* Tooltip */}
                            <div className="opacity-0 group-hover:opacity-100 absolute bottom-full left-1/2 transform -translate-x-1/2 mb-2 bg-gray-800 text-white text-xs rounded py-1 px-2 whitespace-nowrap transition-opacity">
                              Rp {(item.value * 150000).toLocaleString('id-ID')}
                            </div>
                          </div>
                        </div>
                        <div className="mt-2 text-xs text-gray-500 dark:text-gray-400">{item.month}</div>
                      </div>
                    ))}
                  </div>
                </div>
              ) : (
                <div className="overflow-x-auto">
                  <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                    <thead>
                      <tr>
                        <th className="px-6 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                          Bulan
                        </th>
                        <th className="px-6 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                          Pendapatan
                        </th>
                        <th className="px-6 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                          Pertumbuhan
                        </th>
                        <th className="px-6 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                          Pelanggan Baru
                        </th>
                      </tr>
                    </thead>
                    <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                      {tableData.map((item, index) => (
                        <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                          <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                            {item.month}
                          </td>
                          <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                            Rp {item.revenue.toLocaleString('id-ID')}
                          </td>
                          <td className={`px-6 py-4 whitespace-nowrap text-sm ${
                            item.growth.startsWith('+') ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400'
                          } text-right`}>
                            {item.growth}
                          </td>
                          <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                            {item.newCustomers}
                          </td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              )}
            </div>
          </Card>

          {/* Bottom cards */}
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Sumber Pendapatan</Heading>
              <div className="space-y-4">
                <div>
                  <div className="flex justify-between items-center mb-1">
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">Langganan</div>
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">65%</div>
                  </div>
                  <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded-full">
                    <div className="h-2 bg-emerald-500 dark:bg-emerald-600 rounded-full" style={{ width: "65%" }} />
                  </div>
                </div>
                
                <div>
                  <div className="flex justify-between items-center mb-1">
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">Kursus Individual</div>
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">25%</div>
                  </div>
                  <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded-full">
                    <div className="h-2 bg-blue-500 dark:bg-blue-600 rounded-full" style={{ width: "25%" }} />
                  </div>
                </div>
                
                <div>
                  <div className="flex justify-between items-center mb-1">
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">Bundle Kursus</div>
                    <div className="text-sm font-medium text-gray-700 dark:text-gray-300">10%</div>
                  </div>
                  <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded-full">
                    <div className="h-2 bg-purple-500 dark:bg-purple-600 rounded-full" style={{ width: "10%" }} />
                  </div>
                </div>
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Top Produk</Heading>
              <div className="divide-y divide-gray-200 dark:divide-gray-700">
                <div className="flex justify-between items-center py-3">
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">
                    <span className="text-gray-500 dark:text-gray-400 mr-2">1.</span>
                    Paket Premium
                  </div>
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">Rp 45.500.000</div>
                </div>
                <div className="flex justify-between items-center py-3">
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">
                    <span className="text-gray-500 dark:text-gray-400 mr-2">2.</span>
                    Web Development Bundle
                  </div>
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">Rp 32.750.000</div>
                </div>
                <div className="flex justify-between items-center py-3">
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">
                    <span className="text-gray-500 dark:text-gray-400 mr-2">3.</span>
                    JavaScript Masterclass
                  </div>
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">Rp 28.900.000</div>
                </div>
                <div className="flex justify-between items-center py-3">
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">
                    <span className="text-gray-500 dark:text-gray-400 mr-2">4.</span>
                    Data Science Complete
                  </div>
                  <div className="text-sm font-medium text-gray-800 dark:text-gray-200">Rp 18.600.000</div>
                </div>
              </div>
            </Card>
          </div>
        </>
      )}
      
      {/* Metrics Tab Content */}
      {activeTab === 'metrics' && (
        <>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 md:gap-6 mb-6">
            {kpiMetrics.map((metric, index) => (
              <Card key={index} className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
                <div className="flex items-center justify-between mb-2">
                  <Heading level={4} className="text-gray-700 dark:text-gray-300">{metric.name}</Heading>
                  <div className="text-xs text-gray-500 dark:text-gray-400">{metric.description}</div>
                </div>
                <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">{metric.value}</div>
                <div className={`${
                  metric.trend.startsWith('+') ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400'
                } mt-1 flex items-center`}>
                  <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d={
                      metric.trend.startsWith('+') 
                        ? "M5 10l7-7m0 0l7 7m-7-7v18" 
                        : "M19 14l-7 7m0 0l-7-7m7 7V3"
                    } />
                  </svg>
                  {metric.trend} dari periode sebelumnya
                </div>
              </Card>
            ))}
          </div>
          
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="dark:bg-gray-800 dark:border-gray-700 shadow-md overflow-hidden">
              <div className="p-4 md:p-6 border-b border-gray-200 dark:border-gray-700">
                <Heading level={4} className="text-gray-700 dark:text-gray-300">Retensi vs Akuisisi</Heading>
              </div>
              <div className="p-4 md:p-6">
                <div className="h-60 md:h-72 relative">
                  {/* Placeholder for Retention vs Acquisition chart */}
                  <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                    Chart akan ditampilkan di sini
                  </div>
                </div>
              </div>
            </Card>
            
            <Card className="dark:bg-gray-800 dark:border-gray-700 shadow-md overflow-hidden">
              <div className="p-4 md:p-6 border-b border-gray-200 dark:border-gray-700">
                <Heading level={4} className="text-gray-700 dark:text-gray-300">Proyeksi Pendapatan</Heading>
              </div>
              <div className="p-4 md:p-6">
                <div className="h-60 md:h-72 relative">
                  {/* Placeholder for Revenue Projection chart */}
                  <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                    Chart akan ditampilkan di sini
                  </div>
                </div>
              </div>
            </Card>
          </div>
        </>
      )}
            {/* Demographic Tab Content */}
            {activeTab === 'demographic' && (
        <>
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Pendapatan berdasarkan Lokasi</Heading>
              <div className="space-y-4">
                {demographicData.map((item, index) => (
                  <div key={index}>
                    <div className="flex justify-between items-center mb-1">
                      <div className="text-sm font-medium text-gray-700 dark:text-gray-300">
                        {item.location}
                      </div>
                      <div className="text-sm font-medium text-gray-700 dark:text-gray-300">
                        {item.percentage}% - {formatCurrency(item.revenue)}
                      </div>
                    </div>
                    <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded-full">
                      <div 
                        className="h-2 bg-indigo-500 dark:bg-indigo-600 rounded-full" 
                        style={{ width: `${item.percentage}%` }}
                      />
                    </div>
                  </div>
                ))}
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Pendapatan berdasarkan Kelompok Usia</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for Age Group chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Chart akan ditampilkan di sini
                </div>
              </div>
            </Card>
          </div>
          
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Peta Distribusi Pendapatan</Heading>
            <div className="h-96 relative">
              {/* Placeholder for Revenue Distribution Map */}
              <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                Peta akan ditampilkan di sini
              </div>
            </div>
          </Card>
        </>
      )}

            {/* Product Analysis Tab Content */}
            {activeTab === 'product' && (
        <>
          <div className="grid grid-cols-1 lg:grid-cols-3 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Total Kursus</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">35</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +4 dari periode sebelumnya
              </div>
            </Card>
            
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Total Pendaftaran</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">3,785</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +15.2% dari periode sebelumnya
              </div>
            </Card>
            
            <Card className="p-4 md:p-5 dark:bg-gray-800 dark:border-gray-700 shadow-md hover:shadow-lg transition-shadow">
              <Heading level={4} className="text-gray-700 dark:text-gray-300">Tingkat Penyelesaian</Heading>
              <div className="text-2xl font-bold mt-2 text-gray-900 dark:text-white">68.4%</div>
              <div className="text-green-600 dark:text-green-400 mt-1 flex items-center">
                <svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
                </svg>
                +3.7% dari periode sebelumnya
              </div>
            </Card>
          </div>
          
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Pendapatan per Kursus</Heading>
              <div className="overflow-x-auto">
                <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                  <thead>
                    <tr>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Kursus
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Pendapatan
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Pendaftaran
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Penyelesaian
                      </th>
                    </tr>
                  </thead>
                  <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                    {[
                      { name: "JavaScript Masterclass", revenue: 28900000, enrollments: 125, completion: "87%" },
                      { name: "React Framework", revenue: 25600000, enrollments: 110, completion: "82%" },
                      { name: "Data Science Complete", revenue: 18600000, enrollments: 85, completion: "75%" },
                      { name: "Flutter Development", revenue: 16400000, enrollments: 72, completion: "68%" },
                      { name: "Python Programming", revenue: 14250000, enrollments: 65, completion: "79%" },
                    ].map((item, index) => (
                      <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                        <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                          {item.name}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {formatCurrency(item.revenue)}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {item.enrollments}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {item.completion}
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Popularitas Kategori</Heading>
              <div className="space-y-4">
                {[
                  { name: "Web Development", percentage: 35, enrollment: 1325 },
                  { name: "Data Science", percentage: 25, enrollment: 945 },
                  { name: "Mobile Development", percentage: 18, enrollment: 685 },
                  { name: "Cloud Computing", percentage: 12, enrollment: 455 },
                  { name: "DevOps", percentage: 10, enrollment: 375 },
                ].map((item, index) => (
                  <div key={index}>
                    <div className="flex justify-between items-center mb-1">
                      <div className="text-sm font-medium text-gray-700 dark:text-gray-300">
                        {item.name}
                      </div>
                      <div className="text-sm font-medium text-gray-700 dark:text-gray-300">
                        {item.percentage}% ({item.enrollment})
                      </div>
                    </div>
                    <div className="h-2 bg-gray-200 dark:bg-gray-700 rounded-full">
                      <div 
                        className="h-2 bg-indigo-500 dark:bg-indigo-600 rounded-full" 
                        style={{ width: `${item.percentage}%` }}
                      />
                    </div>
                  </div>
                ))}
              </div>
            </Card>
          </div>
          
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Aktivitas Pengguna per Kursus</Heading>
            <div className="h-80 relative">
              {/* Placeholder for User Activity chart */}
              <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                Grafik aktivitas pengguna akan ditampilkan di sini
              </div>
            </div>
          </Card>
        </>
      )}
      {/* Discount Effectiveness Tab Content */}
      {activeTab === 'discount' && (
        <>
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Efektivitas Kupon</Heading>
              <div className="overflow-x-auto">
                <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                  <thead>
                    <tr>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Kode Kupon
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Penggunaan
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Pendapatan
                      </th>
                      <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                        Rata-Rata Order
                      </th>
                    </tr>
                  </thead>
                  <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                    {discountData.map((item, index) => (
                      <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                        <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                          {item.name}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {item.redemptions}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {formatCurrency(item.revenue)}
                        </td>
                        <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                          {formatCurrency(item.avgOrder)}
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Perbandingan Konversi dengan/tanpa Diskon</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for Conversion Comparison chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Chart perbandingan akan ditampilkan di sini
                </div>
              </div>
            </Card>
          </div>
          
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Dampak Diskon pada Margin</Heading>
            <div className="h-60 md:h-72 relative">
              {/* Placeholder for Discount Impact chart */}
              <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                Chart dampak diskon akan ditampilkan di sini
              </div>
            </div>
          </Card>
        </>
      )}
         {/* Marketing ROI Tab Content */}
         {activeTab === 'marketing' && (
        <>
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">ROI Pemasaran berdasarkan Channel</Heading>
            <div className="overflow-x-auto">
              <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                <thead>
                  <tr>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Channel
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Kunjungan
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Konversi
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Rate Konversi
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Pendapatan
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      ROI
                    </th>
                  </tr>
                </thead>
                <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                  {marketingData.map((item, index) => (
                    <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                      <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                        {item.channel}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                        {item.visits.toLocaleString()}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                        {item.conversions.toLocaleString()}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                        {item.convRate}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                        {formatCurrency(item.revenue)}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-green-600 dark:text-green-400 text-right">
                        {item.roi}
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </Card>
          
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Alur Konversi</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for Conversion Funnel chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Alur konversi akan ditampilkan di sini
                </div>
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Evolusi ROI dari Waktu ke Waktu</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for ROI evolution chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Evolusi ROI akan ditampilkan di sini
                </div>
              </div>
            </Card>
          </div>
        </>
      )}
       {/* Advanced Reporting Tab Content */}
       {activeTab === 'advanced' && (
        <>
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Analisis Kohort Retensi</Heading>
            <div className="overflow-x-auto">
              <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                <thead>
                  <tr>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Kohort
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 1
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 2
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 3
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 4
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 5
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Bulan 6
                    </th>
                  </tr>
                </thead>
                <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                  {cohortData.map((item, index) => (
                    <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                      <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                        {item.month}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center bg-green-50 dark:bg-green-900/20">
                        <span className="font-medium text-green-800 dark:text-green-400">{item.m1}</span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center bg-green-50/80 dark:bg-green-900/10">
                        <span className="font-medium text-green-700 dark:text-green-300">{item.m2}</span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center">
                        <span className="font-medium text-green-600 dark:text-green-400">{item.m3}</span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center">
                        <span className="font-medium text-green-500 dark:text-green-500">{item.m4}</span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center">
                        <span className="font-medium text-green-400 dark:text-green-600">{item.m5}</span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-center">
                        <span className="font-medium text-green-300 dark:text-green-700">{item.m6}</span>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </Card>
          
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6 mb-6">
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Segmentasi Pendapatan</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for Revenue Segmentation chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Segmentasi pendapatan akan ditampilkan di sini
                </div>
              </div>
            </Card>
            
            <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-4">Prediksi Arus Kas</Heading>
              <div className="h-60 md:h-72 relative">
                {/* Placeholder for Cash Flow prediction chart */}
                <div className="absolute inset-0 flex items-center justify-center text-gray-400 dark:text-gray-500">
                  Prediksi arus kas akan ditampilkan di sini
                </div>
              </div>
            </Card>
          </div>
          
          <Card className="p-4 md:p-6 dark:bg-gray-800 dark:border-gray-700 shadow-md mb-6">
            <div className="flex items-center justify-between mb-4">
              <Heading level={4} className="text-gray-700 dark:text-gray-300 mb-0">Pengingat Batas Waktu Langganan</Heading>
              <Button size="sm" variant="outline" className="text-xs py-1 px-2 dark:border-gray-600 dark:text-gray-300">
                Lihat Semua
              </Button>
            </div>
            <div className="overflow-x-auto">
              <table className="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                <thead>
                  <tr>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Pelanggan
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Paket
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Berakhir Pada
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-center text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Status
                    </th>
                    <th className="px-4 py-3 bg-gray-50 dark:bg-gray-700 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                      Aksi
                    </th>
                  </tr>
                </thead>
                <tbody className="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                  {[
                    { id: 1, name: "Rizal Fakhri", package: "Premium Annual", expiry: "2023-08-15", status: "critical" },
                    { id: 2, name: "Siti Rahma", package: "Basic Monthly", expiry: "2023-08-22", status: "warning" },
                    { id: 3, name: "Budi Santoso", package: "Professional", expiry: "2023-09-05", status: "normal" },
                    { id: 4, name: "Dewi Lestari", package: "Premium Monthly", expiry: "2023-09-12", status: "normal" },
                  ].map((item, index) => (
                    <tr key={index} className="hover:bg-gray-50 dark:hover:bg-gray-700">
                      <td className="px-4 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">
                        {item.name}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300">
                        {item.package}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-gray-500 dark:text-gray-300 text-right">
                        {item.expiry}
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-center">
                        <span className={`inline-flex px-2 text-xs font-semibold leading-5 rounded-full ${
                          item.status === 'critical'
                            ? 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300'
                            : item.status === 'warning'
                            ? 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-300'
                            : 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300'
                        }`}>
                          {item.status === 'critical' ? 'Kritis' : item.status === 'warning' ? 'Perhatian' : 'Normal'}
                        </span>
                      </td>
                      <td className="px-4 py-4 whitespace-nowrap text-sm text-right">
                        <Button size="sm" className="text-xs py-1 px-2">
                          Kirim Pengingat
                        </Button>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </Card>
        </>
      )}
    </div>
  );
};

export default RevenueAnalytics;

/== organisms/RoadmapFlow.tsx
import React, { useCallback, useState } from 'react';
import ReactFlow, {
  Node,
  Edge,
  Background,
  Controls,
  MiniMap,
  useNodesState,
  useEdgesState,
  addEdge,
  Connection,
  MarkerType
} from 'reactflow';
import 'reactflow/dist/style.css';
import { useRoadmapStore } from '../../store/roadmapStore';
import { Check, Plus } from 'lucide-react';
import Button from '../atoms/Button';

interface RoadmapFlowProps {
  nodes: Node[];
  edges: Edge[];
  onNodeClick?: (nodeId: string) => void;
}

interface AddNodeModalProps {
  isOpen: boolean;
  onClose: () => void;
  onAdd: (title: string, type: 'primary' | 'secondary' | 'highlight') => void;
}

const AddNodeModal: React.FC<AddNodeModalProps> = ({ isOpen, onClose, onAdd }) => {
  const [title, setTitle] = useState('');
  const [type, setType] = useState<'primary' | 'secondary' | 'highlight'>('secondary');

  if (!isOpen) return null;

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onAdd(title, type);
    setTitle('');
    onClose();
  };

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/50">
      <div className="bg-white dark:bg-gray-800 rounded-lg p-6 w-96">
        <h3 className="text-lg font-medium mb-4">Add New Node</h3>
        <form onSubmit={handleSubmit}>
          <div className="mb-4">
            <label className="block text-sm font-medium mb-1">Title</label>
            <input
              type="text"
              value={title}
              onChange={(e) => setTitle(e.target.value)}
              className="w-full px-3 py-2 border rounded-lg"
              required
            />
          </div>
          <div className="mb-4">
            <label className="block text-sm font-medium mb-1">Type</label>
            <select
              value={type}
              onChange={(e) => setType(e.target.value as any)}
              className="w-full px-3 py-2 border rounded-lg"
            >
              <option value="primary">Primary</option>
              <option value="secondary">Secondary</option>
              <option value="highlight">Highlight</option>
            </select>
          </div>
          <div className="flex justify-end space-x-2">
            <Button variant="outline" onClick={onClose}>Cancel</Button>
            <Button variant="primary" type="submit">Add Node</Button>
          </div>
        </form>
      </div>
    </div>
  );
};

const CustomNode = ({ data }: { data: any }) => (
  <div className="relative">
    <div className={`px-4 py-2 rounded-lg font-medium ${data.className}`} style={{ width: '180px' }}>
      {data.label}
      {data.isCompleted && (
        <div className="absolute -top-2 -right-2 w-5 h-5 bg-green-500 rounded-full flex items-center justify-center">
          <Check className="h-3 w-3 text-white" />
        </div>
      )}
    </div>
  </div>
);

const nodeTypes = {
  custom: CustomNode,
};

const getNodeStyle = (type: 'primary' | 'secondary' | 'highlight') => {
  switch (type) {
    case 'primary':
      return 'bg-yellow-100 text-yellow-800 border-2 border-yellow-500';
    case 'highlight':
      return 'bg-purple-100 text-purple-800 border-2 border-purple-500';
    default:
      return 'bg-gray-100 text-gray-800 border-2 border-gray-500';
  }
};

const RoadmapFlow: React.FC<RoadmapFlowProps> = ({
  nodes: initialNodes,
  edges: initialEdges,
  onNodeClick
}) => {
  const [nodes, setNodes, onNodesChange] = useNodesState(initialNodes);
  const [edges, setEdges, onEdgesChange] = useEdgesState(initialEdges);
  const { completedNodes, activeNode } = useRoadmapStore();
  const [isAddingNode, setIsAddingNode] = useState(false);
  const [isConnecting, setIsConnecting] = useState(false);

  // Update nodes to include completion status
  React.useEffect(() => {
    setNodes(
      initialNodes.map(node => ({
        ...node,
        type: 'custom',
        data: {
          ...node.data,
          isCompleted: completedNodes.includes(node.id),
          className: completedNodes.includes(node.id)
            ? 'bg-green-100 text-green-800 border-2 border-green-500'
            : node.id === activeNode
            ? 'bg-blue-100 text-blue-800 border-2 border-blue-500'
            : node.data.className
        }
      }))
    );
  }, [initialNodes, completedNodes, activeNode, setNodes]);

  const onConnect = useCallback(
    (params: Connection) => {
      const newEdge = {
        ...params,
        type: 'smoothstep',
        animated: false,
        style: { stroke: '#94a3b8', strokeWidth: 1 },
        markerEnd: {
          type: MarkerType.ArrowClosed,
          width: 20,
          height: 20,
          color: '#94a3b8',
        },
      };
      setEdges((eds) => addEdge(newEdge, eds));
    },
    [setEdges]
  );

  const handleNodeClick = (_: React.MouseEvent, node: Node) => {
    if (isConnecting) {
      setIsConnecting(false);
    } else {
      onNodeClick?.(node.id);
    }
  };

  const handleAddNode = (title: string, type: 'primary' | 'secondary' | 'highlight') => {
    // Get the center of the viewport
    const centerX = window.innerWidth / 2;
    const centerY = window.innerHeight / 2;
    
    // Create new node with consistent styling
    const newNode = {
      id: `node-${Date.now()}`,
      type: 'custom',
      position: { x: centerX - 90, y: centerY - 20 }, // Center the node, accounting for node width
      data: {
        label: title,
        className: getNodeStyle(type)
      },
      sourcePosition: 'right',
      targetPosition: 'left',
    };
    
    setNodes((nds) => [...nds, newNode]);
  };

  return (
    <div className="h-[800px] w-full bg-gray-50 dark:bg-gray-900 rounded-lg relative">
      <ReactFlow
        nodes={nodes}
        edges={edges}
        onNodesChange={onNodesChange}
        onEdgesChange={onEdgesChange}
        onConnect={onConnect}
        onNodeClick={handleNodeClick}
        nodeTypes={nodeTypes}
        fitView
        attributionPosition="bottom-right"
      >
        <Background />
        <Controls />
        <MiniMap
          nodeColor={(node) => {
            if (completedNodes.includes(node.id)) return '#22c55e';
            if (node.id === activeNode) return '#3b82f6';
            return '#94a3b8';
          }}
        />
      </ReactFlow>

      {/* Custom panel implementation */}
      <div className="absolute top-4 right-4 z-10">
        <div className="flex space-x-2">
          <Button
            variant="primary"
            onClick={() => setIsAddingNode(true)}
            className="flex items-center"
          >
            <Plus className="h-4 w-4 mr-1" />
            Add Node
          </Button>
          <Button
            variant={isConnecting ? 'primary' : 'outline'}
            onClick={() => setIsConnecting(!isConnecting)}
          >
            {isConnecting ? 'Cancel Connection' : 'Connect Nodes'}
          </Button>
        </div>
      </div>

      <AddNodeModal
        isOpen={isAddingNode}
        onClose={() => setIsAddingNode(false)}
        onAdd={handleAddNode}
      />
    </div>
  );
};

export default RoadmapFlow;

/== organisms/RoadmapGrid.tsx
import React from 'react';
import RoadmapSection from '../molecules/RoadmapSection';

interface RoadmapItem {
  id: string;
  title: string;
  isCompleted?: boolean;
  isActive?: boolean;
  type?: 'primary' | 'secondary' | 'highlight';
}

interface RoadmapSection {
  id: string;
  title: string;
  items: RoadmapItem[];
}

interface RoadmapGridProps {
  sections: RoadmapSection[];
  onItemClick?: (sectionId: string, itemId: string) => void;
}

const RoadmapGrid: React.FC<RoadmapGridProps> = ({
  sections,
  onItemClick
}) => {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
      {sections.map((section) => (
        <RoadmapSection
          key={section.id}
          title={section.title}
          items={section.items}
          onItemClick={(itemId) => onItemClick?.(section.id, itemId)}
        />
      ))}
    </div>
  );
};

export default RoadmapGrid;

/== organisms/RoadmapViewer.tsx
import React from 'react';
import { motion } from 'framer-motion';
import RoadmapNodeComponent from '../molecules/RoadmapNodeComponent';
import { RoadmapNode, useRoadmapStore } from '../../store/roadmapStore';

interface RoadmapViewerProps {
  nodes: RoadmapNode[];
  onNodeClick: (node: RoadmapNode) => void;
}

const RoadmapViewer: React.FC<RoadmapViewerProps> = ({
  nodes,
  onNodeClick
}) => {
  const { completedNodes, activeNode } = useRoadmapStore();

  const renderNode = (node: RoadmapNode) => {
    const isCompleted = completedNodes.includes(node.id);
    const isActive = activeNode === node.id;

    return (
      <div key={node.id} className="space-y-4">
        <RoadmapNodeComponent
          node={node}
          onClick={onNodeClick}
          isActive={isActive}
          isCompleted={isCompleted}
        />
        
        {node.children && node.children.length > 0 && (
          <div className="pl-8 space-y-4 border-l-2 border-gray-200">
            {node.children.map((child) => renderNode(child))}
          </div>
        )}
      </div>
    );
  };

  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="space-y-6 p-6"
    >
      {nodes.map((node) => renderNode(node))}
    </motion.div>
  );
};

export default RoadmapViewer;

/== organisms/SubmissionGuidance.tsx
import React, { useState } from 'react';
import { Trophy, Book, Target, Lightbulb, Library, MoreHorizontal } from 'lucide-react';
import SubmissionTab from '../molecules/SubmissionTab';
import CriteriaItem from '../molecules/CriteriaItem';
import SubmissionResource from '../molecules/SubmissionResource';

interface TabContentProps {
  type: 'introduction' | 'criteria' | 'evaluation' | 'resources' | 'tips' | 'others';
}

const TabContent: React.FC<TabContentProps> = ({ type }) => {
  switch (type) {
    case 'introduction':
      return (
        <div className="space-y-6">
          <div className="bg-gradient-to-r from-blue-500 to-purple-600 rounded-lg p-6 text-white">
            <h2 className="text-2xl font-bold mb-4">Welcome to Your Final Project!</h2>
            <p className="text-lg opacity-90">
              Congratulations on reaching this milestone! This project will challenge you to apply everything 
              you've learned about Go programming. You'll be building a real-world application that demonstrates 
              your mastery of key concepts.
            </p>
          </div>
          
          <div className="bg-white dark:bg-gray-800 rounded-lg p-6 space-y-4">
            <h3 className="text-xl font-semibold text-gray-900 dark:text-white">Project Overview</h3>
            <p className="text-gray-600 dark:text-gray-300">
              You'll be developing a complete web application that showcases your understanding of:
            </p>
            <ul className="space-y-2 text-gray-600 dark:text-gray-300">
              <li className="flex items-center">
                <span className="w-2 h-2 bg-blue-500 rounded-full mr-2"></span>
                Core Go concepts and syntax
              </li>
              <li className="flex items-center">
                <span className="w-2 h-2 bg-blue-500 rounded-full mr-2"></span>
                Web server implementation
              </li>
              <li className="flex items-center">
                <span className="w-2 h-2 bg-blue-500 rounded-full mr-2"></span>
                Database integration
              </li>
              <li className="flex items-center">
                <span className="w-2 h-2 bg-blue-500 rounded-full mr-2"></span>
                API development
              </li>
            </ul>
          </div>
        </div>
      );

    case 'criteria':
      return (
        <div className="space-y-4">
          <CriteriaItem
            title="Project Structure"
            description="Well-organized code following Go best practices and project layout conventions"
            points={20}
          />
          <CriteriaItem
            title="Core Functionality"
            description="Implementation of all required features with proper error handling"
            points={30}
          />
          <CriteriaItem
            title="Code Quality"
            description="Clean, maintainable code with appropriate comments and documentation"
            points={25}
          />
          <CriteriaItem
            title="Testing"
            description="Comprehensive test coverage including unit and integration tests"
            points={15}
          />
          <CriteriaItem
            title="Documentation"
            description="Clear README and API documentation"
            points={10}
          />
        </div>
      );

    case 'evaluation':
      return (
        <div className="space-y-6">
          <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
            <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-4">
              Evaluation Process
            </h3>
            <div className="space-y-4">
              <div className="flex items-start space-x-4">
                <div className="w-8 h-8 rounded-full bg-blue-100 dark:bg-blue-900 flex items-center justify-center flex-shrink-0">
                  <span className="text-blue-600 dark:text-blue-400 font-semibold">1</span>
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Code Review</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Your code will be reviewed for structure, quality, and best practices
                  </p>
                </div>
              </div>
              
              <div className="flex items-start space-x-4">
                <div className="w-8 h-8 rounded-full bg-green-100 dark:bg-green-900 flex items-center justify-center flex-shrink-0">
                  <span className="text-green-600 dark:text-green-400 font-semibold">2</span>
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Testing</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Automated tests will verify functionality and performance
                  </p>
                </div>
              </div>
              
              <div className="flex items-start space-x-4">
                <div className="w-8 h-8 rounded-full bg-purple-100 dark:bg-purple-900 flex items-center justify-center flex-shrink-0">
                  <span className="text-purple-600 dark:text-purple-400 font-semibold">3</span>
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Documentation</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Review of project documentation and API specifications
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      );

    case 'resources':
      return (
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          <SubmissionResource
            title="Go Documentation"
            description="Official Go programming language documentation and guides"
            link="https://golang.org/doc/"
          />
          <SubmissionResource
            title="Testing in Go"
            description="Learn about testing best practices in Go"
            link="https://golang.org/pkg/testing/"
          />
          <SubmissionResource
            title="Project Layout"
            description="Standard Go project layout and organization"
            link="https://github.com/golang-standards/project-layout"
          />
          <SubmissionResource
            title="API Design"
            description="RESTful API design guidelines and best practices"
            link="https://golang.org/doc/tutorial/web-service-gin"
          />
        </div>
      );

    case 'tips':
      return (
        <div className="space-y-6">
          <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
            <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-4">
              Tips for Success
            </h3>
            <div className="space-y-4">
              <div className="flex items-start space-x-4">
                <div className="flex-shrink-0">
                  <Lightbulb className="h-6 w-6 text-yellow-500" />
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Plan First</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Spend time planning your project structure and features before coding
                  </p>
                </div>
              </div>
              
              <div className="flex items-start space-x-4">
                <div className="flex-shrink-0">
                  <Target className="h-6 w-6 text-red-500" />
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Start Small</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Begin with core features and gradually add complexity
                  </p>
                </div>
              </div>
              
              <div className="flex items-start space-x-4">
                <div className="flex-shrink-0">
                  <Book className="h-6 w-6 text-blue-500" />
                </div>
                <div>
                  <h4 className="text-lg font-medium text-gray-900 dark:text-white">Document As You Go</h4>
                  <p className="text-gray-600 dark:text-gray-300">
                    Keep documentation updated while developing, don't leave it for the end
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      );

    case 'others':
      return (
        <div className="space-y-6">
          <div className="bg-white dark:bg-gray-800 rounded-lg p-6">
            <h3 className="text-xl font-semibold text-gray-900 dark:text-white mb-4">
              Additional Information
            </h3>
            <div className="space-y-4 text-gray-600 dark:text-gray-300">
              <p>
                Remember to check the submission deadline and requirements carefully.
                If you have any questions, don't hesitate to ask in the discussion forum.
              </p>
              <div className="bg-blue-50 dark:bg-blue-900/50 p-4 rounded-lg">
                <h4 className="font-medium text-blue-800 dark:text-blue-200 mb-2">
                  Need Help?
                </h4>
                <p className="text-blue-600 dark:text-blue-300">
                  Join our community discussion or contact a mentor for guidance.
                </p>
              </div>
            </div>
          </div>
        </div>
      );

    default:
      return null;
  }
};

const SubmissionGuidance: React.FC = () => {
  const [activeTab, setActiveTab] = useState<
    'introduction' | 'criteria' | 'evaluation' | 'resources' | 'tips' | 'others'
  >('introduction');

  const tabs = [
    { id: 'introduction', label: 'Introduction', icon: Trophy },
    { id: 'criteria', label: 'Criteria', icon: Target },
    { id: 'evaluation', label: 'Evaluation', icon: Book },
    { id: 'resources', label: 'Resources', icon: Library },
    { id: 'tips', label: 'Tips & Tricks', icon: Lightbulb },
    { id: 'others', label: 'Others', icon: MoreHorizontal }
  ] as const;

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="bg-white dark:bg-gray-800 rounded-xl shadow-sm overflow-hidden">
          <div className="border-b border-gray-200 dark:border-gray-700">
            <div className="flex overflow-x-auto">
              {tabs.map((tab) => (
                <SubmissionTab
                  key={tab.id}
                  label={tab.label}
                  isActive={activeTab === tab.id}
                  onClick={() => setActiveTab(tab.id)}
                />
              ))}
            </div>
          </div>
          
          <div className="p-6">
            <TabContent type={activeTab} />
          </div>
        </div>
      </div>
    </div>
  );
};

export default SubmissionGuidance;

/== organisms/SubmissionReview.tsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import SubmissionStatus from '../molecules/SubmissionStatus';
import ReviewFeedback from '../molecules/ReviewFeedback';
import Button from '../atoms/Button';

interface SubmissionReviewProps {
  status: 'reviewing' | 'rejected' | 'approved';
  feedback?: {
    type: 'success' | 'error';
    message: string;
  }[];
  reviewerNotes?: string;
  submissionFiles?: {
    name: string;
    path: string;
    comments: number;
  }[];
  onResubmit?: () => void;
}

const SubmissionReview: React.FC<SubmissionReviewProps> = ({
  status,
  feedback = [],
  reviewerNotes,
  submissionFiles,
  onResubmit
}) => {
  const navigate = useNavigate();
  const [isResubmitting, setIsResubmitting] = useState(false);

  const handleResubmit = async () => {
    if (onResubmit) {
      setIsResubmitting(true);
      await onResubmit();
      setIsResubmitting(false);
    }
  };

  const statusMessages = {
    reviewing: 'Your submission is currently being reviewed. We will notify you once the review is complete.',
    rejected: 'Your submission needs some improvements. Please review the feedback below and resubmit.',
    approved: 'Congratulations! Your submission has been approved.'
  };

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <div className="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="space-y-8">
          {/* Status Banner */}
          <SubmissionStatus 
            status={status} 
            message={statusMessages[status]} 
          />

          {/* Review Feedback */}
          {(status === 'rejected' || status === 'approved') && (
            <ReviewFeedback
              feedback={feedback}
              reviewerNotes={reviewerNotes}
              submissionFiles={submissionFiles}
            />
          )}

          {/* Action Buttons */}
          <div className="flex justify-end space-x-4">
            <Button
              variant="outline"
              onClick={() => navigate('/dashboard')}
              className="text-gray-600 hover:text-gray-900"
            >
              Back to Dashboard
            </Button>
            
            {status === 'rejected' && (
              <Button
                variant="primary"
                onClick={handleResubmit}
                disabled={isResubmitting}
                className="bg-blue-600 hover:bg-blue-700"
              >
                {isResubmitting ? 'Preparing Resubmission...' : 'Resubmit Project'}
              </Button>
            )}
          </div>
        </div>
      </div>
    </div>
  );
};

export default SubmissionReview;

/== organisms/SubscriptionList.tsx
import React, { useState, useEffect } from 'react';
import SubscriptionCard from '../../components/molecules/SubscriptionCard';
import Toast from '../atoms/Toast';
import { useSubscriptionStore } from '../../store/subscriptionStore';

const SubscriptionList: React.FC = () => {
  const { subscriptions, deleteSubscription, updateSubscription, settings, updateSettings } = useSubscriptionStore();
  const [toast, setToast] = useState({ 
    show: false, 
    message: '', 
    type: 'info' 
  });
  
  const [billingCycle, setBillingCycle] = useState('yearly'); // 'yearly' or 'monthly'
  
  // State for editable texts
  const [editingDiscount, setEditingDiscount] = useState(false);
  const [editingRegionText, setEditingRegionText] = useState(false);
  const [editingYearlyButton, setEditingYearlyButton] = useState(false);
  const [editingMonthlyButton, setEditingMonthlyButton] = useState(false);
  
  const [discountText, setDiscountText] = useState(settings?.discountText || 'Save 66%');
  const [regionText, setRegionText] = useState(settings?.regionText || 'Price discounted based on your region');
  const [yearlyButtonText, setYearlyButtonText] = useState(settings?.yearlyButtonText || 'Yearly');
  const [monthlyButtonText, setMonthlyButtonText] = useState(settings?.monthlyButtonText || 'Monthly');
  
  // Initialize settings from store when component mounts
  useEffect(() => {
    if (settings) {
      setDiscountText(settings.discountText || 'Save 66%');
      setRegionText(settings.regionText || 'Price discounted based on your region');
      setYearlyButtonText(settings.yearlyButtonText || 'Yearly');
      setMonthlyButtonText(settings.monthlyButtonText || 'Monthly');
    }
  }, [settings]);
  
  const handleDelete = (subscriptionId: string) => {
    deleteSubscription(subscriptionId);
    setToast({ 
      show: true, 
      message: 'Paket langganan berhasil dihapus', 
      type: 'success' 
    });
  };
  
  const handleUpdate = (id: string, updatedData: any) => {
    updateSubscription(id, updatedData);
    setToast({
      show: true,
      message: 'Paket langganan berhasil diperbarui',
      type: 'success'
    });
  };
  
  const handleSubscribe = (subscriptionName: string) => {
    setToast({
      show: true,
      message: `Berlangganan paket ${subscriptionName} berhasil`,
      type: 'success'
    });
  };
  
  // Handle saving text changes
  const handleSaveDiscountText = () => {
    updateSettings({ ...settings, discountText });
    setEditingDiscount(false);
    setToast({
      show: true,
      message: 'Teks diskon berhasil diperbarui',
      type: 'success'
    });
  };
  
  const handleSaveRegionText = () => {
    updateSettings({ ...settings, regionText });
    setEditingRegionText(false);
    setToast({
      show: true,
      message: 'Teks region berhasil diperbarui',
      type: 'success'
    });
  };
  
  const handleSaveYearlyButtonText = () => {
    updateSettings({ ...settings, yearlyButtonText });
    setEditingYearlyButton(false);
    setToast({
      show: true,
      message: 'Teks tombol tahunan berhasil diperbarui',
      type: 'success'
    });
  };
  
  const handleSaveMonthlyButtonText = () => {
    updateSettings({ ...settings, monthlyButtonText });
    setEditingMonthlyButton(false);
    setToast({
      show: true,
      message: 'Teks tombol bulanan berhasil diperbarui',
      type: 'success'
    });
  };

  return (
    <div className="max-w-6xl mx-auto px-4 py-8">
      {/* Billing cycle toggle */}
      <div className="flex items-center justify-center gap-4 mb-8">
        {/* Editable Yearly Button */}
        <div className={`relative ${billingCycle === 'yearly' ? 'bg-green-500 text-white' : 'bg-gray-200 text-gray-800 dark:text-gray-800'} rounded-lg overflow-hidden`}>
          {editingYearlyButton ? (
            <input
              type="text"
              value={yearlyButtonText}
              onChange={(e) => setYearlyButtonText(e.target.value)}
              className={`px-6 py-2.5 w-full text-center ${
                billingCycle === 'yearly' 
                  ? 'bg-green-500 text-white border-green-400' 
                  : 'bg-gray-200 text-gray-800 border-gray-300'
              } border focus:outline-none`}
              autoFocus
              onBlur={handleSaveYearlyButtonText}
              onKeyPress={(e) => e.key === 'Enter' && handleSaveYearlyButtonText()}
            />
          ) : (
            <button
              className="px-6 py-2.5 w-full h-full relative group"
              onClick={() => setBillingCycle('yearly')}
              onDoubleClick={() => setEditingYearlyButton(true)}
            >
              <span>{yearlyButtonText}</span>
              <div className="absolute inset-0 bg-black opacity-0 group-hover:opacity-10 transition-opacity cursor-text" />
              <div className="absolute bottom-0 left-0 right-0 h-0.5 bg-blue-500 opacity-0 group-hover:opacity-100 transition-opacity"></div>
            </button>
          )}
        </div>
        
        {/* Editable Monthly Button */}
        <div className={`relative ${billingCycle === 'monthly' ? 'bg-green-500 text-white' : 'bg-gray-200 text-gray-800 dark:text-gray-800'} rounded-lg overflow-hidden`}>
          {editingMonthlyButton ? (
            <input
              type="text"
              value={monthlyButtonText}
              onChange={(e) => setMonthlyButtonText(e.target.value)}
              className={`px-6 py-2.5 w-full text-center ${
                billingCycle === 'monthly' 
                  ? 'bg-green-500 text-white border-green-400' 
                  : 'bg-gray-200 text-gray-800 border-gray-300'
              } border focus:outline-none`}
              autoFocus
              onBlur={handleSaveMonthlyButtonText}
              onKeyPress={(e) => e.key === 'Enter' && handleSaveMonthlyButtonText()}
            />
          ) : (
            <button
              className="px-6 py-2.5 w-full h-full relative group"
              onClick={() => setBillingCycle('monthly')}
              onDoubleClick={() => setEditingMonthlyButton(true)}
            >
              <span>{monthlyButtonText}</span>
              <div className="absolute inset-0 bg-black opacity-0 group-hover:opacity-10 transition-opacity cursor-text" />
              <div className="absolute bottom-0 left-0 right-0 h-0.5 bg-blue-500 opacity-0 group-hover:opacity-100 transition-opacity"></div>
            </button>
          )}
        </div>
        
        {/* Editable discount text */}
        {editingDiscount ? (
          <div className="relative">
            <input
              type="text"
              value={discountText}
              onChange={(e) => setDiscountText(e.target.value)}
              className="bg-green-100 text-green-800 px-3 py-1 rounded-lg text-sm font-medium border border-green-300 w-32"
              autoFocus
              onBlur={handleSaveDiscountText}
              onKeyPress={(e) => e.key === 'Enter' && handleSaveDiscountText()}
            />
          </div>
        ) : (
          <span 
            className="bg-green-100 text-green-800 px-3 py-1 rounded-lg text-sm font-medium cursor-pointer hover:bg-green-200 transition-colors"
            onClick={() => setEditingDiscount(true)}
          >
            {discountText}
          </span>
        )}
      </div>
      
      {/* Editable price discounted badge */}
      <div className="flex items-center justify-center mb-10 text-gray-600">
        <svg className="w-5 h-5 mr-2 flex-shrink-0" viewBox="0 0 24 24" fill="none">
          <circle cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="2" />
          <path d="M12 8v4m0 4h.01" stroke="currentColor" strokeWidth="2" strokeLinecap="round" />
        </svg>
        
        {editingRegionText ? (
          <div className="relative">
            <input
              type="text"
              value={regionText}
              onChange={(e) => setRegionText(e.target.value)}
              className="text-gray-800 dark:text-white border border-gray-300 dark:border-gray-600 rounded px-2 py-1 w-full"
              autoFocus
              onBlur={handleSaveRegionText}
              onKeyPress={(e) => e.key === 'Enter' && handleSaveRegionText()}
            />
          </div>
        ) : (
          <span 
            className="text-gray-800 dark:text-white cursor-pointer hover:text-blue-600 dark:hover:text-blue-400 transition-colors"
            onClick={() => setEditingRegionText(true)}
          >
            {regionText}
          </span>
        )}
      </div>
      
      {/* Subscription cards */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        {subscriptions.map((subscription) => (
          <SubscriptionCard
            key={subscription.id}
            id={subscription.id}
            type={subscription.type}
            title={subscription.title}
            subtitle={subscription.subtitle}
            price={billingCycle === 'yearly' ? subscription.yearlyPrice : subscription.monthlyPrice}
            pricePeriod={billingCycle === 'yearly' ? 'year' : 'month'}
            features={subscription.features}
            buttonText={subscription.buttonText}
            isPopular={subscription.isPopular}
            onSubscribe={() => handleSubscribe(subscription.title)}
            onDelete={() => handleDelete(subscription.id)}
            onUpdate={(updatedData) => handleUpdate(subscription.id, updatedData)}
            guarantee={subscription.guarantee}
          />
        ))}
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

export default SubscriptionList;

/== organisms/TutorialContent.tsx
import React from 'react';
import { ChevronRight } from 'lucide-react';
import Card from '../atoms/Card';
import Button from '../atoms/Button';
import IconText from '../molecules/IconText';

interface TutorialContentProps {
  title: string;
  introduction: string;
  benefits: string[];
  topics: { title: string; path: string }[];
  resources: {
    icon: any;
    title: string;
    description: string;
    link: string;
    linkText: string;
    bgColor: string;
  }[];
  gettingStarted: string[];
  language: string;
}

const TutorialContent: React.FC<TutorialContentProps> = ({
  title,
  introduction,
  benefits,
  topics,
  resources,
  gettingStarted,
  language
}) => {
  return (
    <div className="grid grid-cols-1 lg:grid-cols-4 gap-8">
      {/* Sidebar */}
      <div className="lg:col-span-1">
        <Card className="p-6 sticky top-20">
          <h3 className="text-lg font-bold text-gray-900 mb-4">Table of Contents</h3>
          <ul className="space-y-2">
            {topics.map((topic) => (
              <li key={topic.path}>
                <IconText
                  icon={ChevronRight}
                  text={topic.title}
                  className="text-gray-700 hover:text-blue-600 cursor-pointer"
                />
              </li>
            ))}
          </ul>
        </Card>
      </div>

      {/* Main Content */}
      <div className="lg:col-span-3">
        <Card className="p-6 mb-8">
          <h2 className="text-2xl font-bold text-gray-900 mb-4">
            Introduction to {language?.toUpperCase()}
          </h2>
          <div className="prose max-w-none">
            <p>{introduction}</p>
            
            <h3>Why Learn {language?.toUpperCase()}?</h3>
            <ul>
              {benefits.map((benefit, index) => (
                <li key={index}>{benefit}</li>
              ))}
            </ul>
          </div>
        </Card>

        {/* Resources */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
          {resources.map((resource, index) => (
            <Card key={index} className={`p-6 ${resource.bgColor}`}>
              <IconText
                icon={resource.icon}
                text={resource.title}
                className="mb-4 font-medium text-gray-900"
              />
              <p className="text-gray-700 mb-4">{resource.description}</p>
              <Button
                variant="primary"
                onClick={() => window.location.href = resource.link}
              >
                {resource.linkText}
              </Button>
            </Card>
          ))}
        </div>

        {/* Getting Started */}
        <Card className="p-6">
          <h2 className="text-2xl font-bold text-gray-900 mb-4">Getting Started</h2>
          <div className="prose max-w-none">
            <p>Ready to start learning {language?.toUpperCase()}? Here's what you need to do:</p>
            <ol>
              {gettingStarted.map((step, index) => (
                <li key={index}>{step}</li>
              ))}
            </ol>
            <div className="mt-6">
              <Button
                variant="primary"
                onClick={() => window.location.href = topics[0]?.path || '#'}
              >
                Start with the First Tutorial
              </Button>
            </div>
          </div>
        </Card>
      </div>
    </div>
  );
};

export default TutorialContent;

/== organisms/TutorialHeader.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { ChevronRight } from 'lucide-react';
import Text from '../atoms/Text';

interface TutorialHeaderProps {
  language: string;
  title: string;
}

const TutorialHeader: React.FC<TutorialHeaderProps> = ({ language, title }) => {
  return (
    <div className="bg-gray-100 py-3">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex items-center text-sm text-gray-600">
          <Link to="/" className="hover:text-blue-600">Home</Link>
          <ChevronRight className="h-4 w-4 mx-2" />
          <Link to="/tutorials" className="hover:text-blue-600">Tutorials</Link>
          <ChevronRight className="h-4 w-4 mx-2" />
          <Text variant="body" color="default" className="font-medium">{title}</Text>
        </div>
      </div>
    </div>
  );
};

export default TutorialHeader;

/== organisms/TutorialIntroduction.tsx
import React from 'react';
import Card from '../atoms/Card';
import Text from '../atoms/Text';
import Heading from '../atoms/Heading';

interface TutorialIntroductionProps {
  title: string;
  introduction: string;
  benefits: string[];
  language: string;
}

const TutorialIntroduction: React.FC<TutorialIntroductionProps> = ({
  title,
  introduction,
  benefits,
  language
}) => {
  return (
    <Card className="p-6 mb-8">
      <Heading level={2} className="mb-4">Introduction to {language?.toUpperCase()}</Heading>
      <div className="prose max-w-none">
        <Text variant="body" color="muted" className="mb-6">{introduction}</Text>
        
        <Heading level={3} className="mb-4">Why Learn {language?.toUpperCase()}?</Heading>
        <ul className="space-y-2">
          {benefits.map((benefit, index) => (
            <li key={index} className="flex items-start">
              <span className="text-blue-600 mr-2">•</span>
              <Text variant="body" color="muted">{benefit}</Text>
            </li>
          ))}
        </ul>
      </div>
    </Card>
  );
};

export default TutorialIntroduction;

/== organisms/TutorialResources.tsx
import React from 'react';
import ResourceCard from '../molecules/ResourceCard';

interface TutorialResourcesProps {
  resources: {
    icon: any;
    title: string;
    description: string;
    link: string;
    linkText: string;
    bgColor: string;
  }[];
}

const TutorialResources: React.FC<TutorialResourcesProps> = ({ resources }) => {
  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
      {resources.map((resource, index) => (
        <ResourceCard
          key={index}
          icon={resource.icon}
          title={resource.title}
          description={resource.description}
          link={resource.link}
          linkText={resource.linkText}
          bgColor={resource.bgColor}
        />
      ))}
    </div>
  );
};

export default TutorialResources;

/== templates/AdminLayout.tsx
import React, { useState } from 'react';
import { Outlet, Link, useLocation, useNavigate } from 'react-router-dom';
import { Menu, X, ChevronDown, Home, Book, Users, Settings, LogOut, FileText, Image, Edit, Layout, PlayCircle, MessageSquare, Bell, MessageCircle, Tag, Calendar, Award, Star, Shield, Activity, TrendingUp, Globe, CreditCard, Server, Webhook, DollarSign, Gift, Package, Users2, PieChart } from 'lucide-react';
import Logo from '../atoms/Logo';
import { useAuthStore } from '../../store/authStore';
import { useThemeStore } from '../../store/themeStore';

const AdminLayout: React.FC = () => {
  const [isSidebarOpen, setIsSidebarOpen] = useState(true);
  const [isContentMenuOpen, setIsContentMenuOpen] = useState(false);
  const [isCommunicationMenuOpen, setIsCommunicationMenuOpen] = useState(false);
  const [isCourseFeaturesMenuOpen, setIsCourseFeaturesMenuOpen] = useState(false);
  const [isUserManagementMenuOpen, setIsUserManagementMenuOpen] = useState(false);
  const [isIntegrationsMenuOpen, setIsIntegrationsMenuOpen] = useState(false);
  const [isMonetizationMenuOpen, setIsMonetizationMenuOpen] = useState(false);
  const location = useLocation();
  const navigate = useNavigate();
  const { logout } = useAuthStore();
  const { isDarkMode, toggleTheme } = useThemeStore();

  const menuItems = [
    { icon: Home, label: 'Dashboard', path: '/cms' },
    { icon: Book, label: 'Courses', path: '/cms/courses' },
    {
      icon: Globe,
      label: 'Integrasi',
      children: [
        { icon: Layout, label: 'Dashboard', path: '/cms/integrations' },
        { icon: CreditCard, label: 'Payment Gateways', path: '/cms/integrations/payment-gateways' },
        { icon: Shield, label: 'SSO', path: '/cms/integrations/sso' },
        { icon: Server, label: 'API Management', path: '/cms/integrations/api' },
        { icon: Webhook, label: 'Webhooks', path: '/cms/integrations/webhooks' },
        { icon: Globe, label: 'Third Party', path: '/cms/integrations/third-party' },
      ]
    },
    {
      icon: FileText,
      label: 'Content',
      children: [
        { icon: Edit, label: 'Content Editor', path: '/cms/content/editor' },
        { icon: Image, label: 'Media Library', path: '/cms/media' },
        { icon: PlayCircle, label: 'Quiz Builder', path: '/cms/quizzes' },
        { icon: Layout, label: 'Curriculum Builder', path: '/cms/curriculum' },
      ]
    },
    {
      icon: MessageSquare,
      label: 'Communication',
      children: [
        { icon: MessageSquare, label: 'Messaging', path: '/cms/communication/messaging' },
        { icon: FileText, label: 'Email Templates', path: '/cms/communication/email-templates' },
        { icon: Layout, label: 'Announcements', path: '/cms/communication/announcements' },
        { icon: Bell, label: 'Notifications', path: '/cms/communication/notifications' },
        { icon: MessageCircle, label: 'Feedback', path: '/cms/communication/feedback' },
      ]
    },
    {
      icon: Layout,
      label: 'Course Features',
      children: [
        { icon: Tag, label: 'Categories', path: '/cms/course-features/categories' },
        { icon: Calendar, label: 'Scheduling', path: '/cms/course-features/scheduling' },
        { icon: Book, label: 'Prerequisites', path: '/cms/course-features/prerequisites' },
        { icon: Award, label: 'Certificates', path: '/cms/course-features/certificates' },
        { icon: Star, label: 'Reviews', path: '/cms/course-features/reviews' },
      ]
    },
    {
      icon: Users,
      label: 'User Management',
      children: [
        { icon: Users, label: 'Users', path: '/cms/users' },
        { icon: Shield, label: 'Roles & Permissions', path: '/cms/user-management/roles' },
        { icon: Activity, label: 'User Activity', path: '/cms/user-management/activity' },
        { icon: TrendingUp, label: 'User Progress', path: '/cms/user-management/progress' },
      ]
    },
    {
      icon: DollarSign,
      label: 'Monetisasi',
      children: [
        { icon: Tag, label: 'Dashboard', path: '/cms/monetization' },
        { icon: CreditCard, label: 'Manajemen Langganan', path: '/cms/monetization/subscription' },
        { icon: Gift, label: 'Diskon & Kupon', path: '/cms/monetization/coupons' },
        { icon: Package, label: 'Bundling Kursus', path: '/cms/monetization/bundles' },
        { icon: Users2, label: 'Afiliasi & Referral', path: '/cms/monetization/affiliates' },
        { icon: PieChart, label: 'Analitik Pendapatan', path: '/cms/monetization/revenue-analytics' },
      ]
    },
    { icon: Settings, label: 'Settings', path: '/cms/settings' },
  ];

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      {/* Sidebar */}
      <aside className={`
        fixed top-0 left-0 z-40 w-64 h-screen 
        ${isSidebarOpen ? 'translate-x-0' : '-translate-x-full'}
        bg-white dark:bg-gray-800 border-r border-gray-200 dark:border-gray-700
        transition-transform flex flex-col
      `}>
        {/* Fixed Header */}
        <div className="flex items-center justify-between p-4 border-b border-gray-200 dark:border-gray-700">
          <Logo showText={false} />
          <button onClick={() => setIsSidebarOpen(false)} className="lg:hidden">
            <X className="h-6 w-6 text-gray-500" />
          </button>
        </div>
        
        {/* Scrollable Content */}
        <div className="flex-1 overflow-y-auto">
          <nav className="p-4 space-y-2">
            {menuItems.map((item) =>
              item.children ? (
                <div key={item.label}>
                  <button
                    onClick={() => {
                      if (item.label === 'Content') {
                        setIsContentMenuOpen(!isContentMenuOpen);
                      } else if (item.label === 'Communication') {
                        setIsCommunicationMenuOpen(!isCommunicationMenuOpen);
                      } else if (item.label === 'Course Features') {
                        setIsCourseFeaturesMenuOpen(!isCourseFeaturesMenuOpen);
                      } else if (item.label === 'User Management') {
                        setIsUserManagementMenuOpen(!isUserManagementMenuOpen);
                      } else if (item.label === 'Integrasi') {
                        setIsIntegrationsMenuOpen(!isIntegrationsMenuOpen);
                      } else if (item.label === 'Monetisasi') {
                        setIsMonetizationMenuOpen(!isMonetizationMenuOpen);
                      }
                    }}
                    className={`
                      w-full flex items-center justify-between px-4 py-2 text-sm font-medium rounded-lg
                      ${(item.label === 'Content' && (location.pathname.startsWith('/cms/content') || location.pathname.startsWith('/cms/media') || location.pathname.startsWith('/cms/quizzes') || location.pathname.startsWith('/cms/curriculum'))) ||
                        (item.label === 'Communication' && location.pathname.startsWith('/cms/communication')) ||
                        (item.label === 'Course Features' && location.pathname.startsWith('/cms/course-features')) ||
                        (item.label === 'User Management' && location.pathname.startsWith('/cms/user-management')) ||
                        (item.label === 'Integrasi' && location.pathname.startsWith('/cms/integrations')) ||
                        (item.label === 'Monetisasi' && location.pathname.startsWith('/cms/monetization'))
                        ? 'bg-brand-green text-white'
                        : 'text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700'
                      }
                    `}
                  >
                    <div className="flex items-center">
                      <item.icon className="h-5 w-5 mr-3" />
                      {item.label}
                    </div>
                    <ChevronDown className={`h-4 w-4 transition-transform ${
                      (item.label === 'Content' && isContentMenuOpen) ||
                      (item.label === 'Communication' && isCommunicationMenuOpen) ||
                      (item.label === 'Course Features' && isCourseFeaturesMenuOpen) ||
                      (item.label === 'User Management' && isUserManagementMenuOpen) ||
                      (item.label === 'Integrasi' && isIntegrationsMenuOpen) ||
                      (item.label === 'Monetisasi' && isMonetizationMenuOpen)
                        ? 'rotate-180'
                        : ''
                    }`} />
                  </button>
                  {((item.label === 'Content' && isContentMenuOpen) ||
                    (item.label === 'Communication' && isCommunicationMenuOpen) ||
                    (item.label === 'Course Features' && isCourseFeaturesMenuOpen) ||
                    (item.label === 'User Management' && isUserManagementMenuOpen) ||
                    (item.label === 'Integrasi' && isIntegrationsMenuOpen) ||
                    (item.label === 'Monetisasi' && isMonetizationMenuOpen)) && (
                    <div className="mt-2 ml-6 space-y-2">
                      {item.children.map((child) => (
                        <Link
                          key={child.path}
                          to={child.path}
                          className={`
                            flex items-center px-4 py-2 text-sm font-medium rounded-lg
                            ${location.pathname.startsWith(child.path)
                              ? 'bg-brand-green/10 text-brand-green'
                              : 'text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700'
                            }
                          `}
                        >
                          <child.icon className="h-4 w-4 mr-3" />
                          {child.label}
                        </Link>
                      ))}
                    </div>
                  )}
                </div>
              ) : (
                <Link
                  key={item.path}
                  to={item.path}
                  className={`
                    flex items-center px-4 py-2 rounded-lg text-sm font-medium transition-colors duration-200
                    ${location.pathname === item.path
                      ? 'bg-brand-green text-white'
                      : 'text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700'
                    }
                  `}
                >
                  <item.icon className="h-5 w-5 mr-3" />
                  {item.label}
                </Link>
              )
            )}
          </nav>
        </div>
      </aside>

      {/* Main Content */}
      <div className={`lg:ml-64 ${isSidebarOpen ? 'ml-64' : 'ml-0'}`}>
        {/* Header */}
        <header className="bg-white dark:bg-gray-800 border-b border-gray-200 dark:border-gray-700">
          <div className="flex items-center justify-between px-4 py-3">
            <button
              onClick={() => setIsSidebarOpen(!isSidebarOpen)}
              className="lg:hidden"
            >
              <Menu className="h-6 w-6 text-gray-500" />
            </button>
            
            <div className="flex items-center space-x-4">
              <button
                onClick={toggleTheme}
                className="p-2 text-gray-500 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700"
              >
                {isDarkMode ? (
                  <svg className="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
                  </svg>
                ) : (
                  <svg className="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
                  </svg>
                )}
              </button>
              <button
                onClick={handleLogout}
                className="flex items-center text-gray-700 dark:text-gray-300 hover:text-gray-900 dark:hover:text-white"
              >
                <LogOut className="h-5 w-5 mr-2" />
                Logout
              </button>
            </div>
          </div>
        </header>

        {/* Page Content */}
        <main className="p-4">
          <Outlet />
        </main>
      </div>
    </div>
  );
};

export default AdminLayout;

/== templates/AuthLayout.tsx
import React from 'react';

interface AuthLayoutProps {
  children: React.ReactNode;
}

const AuthLayout: React.FC<AuthLayoutProps> = ({ children }) => {
  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900 py-12 px-4 sm:px-6 lg:px-8">
      <div className="max-w-md w-full mx-auto">
        {children}
      </div>
    </div>
  );
};

export default AuthLayout;

/== templates/CustomerLayout.tsx


/== templates/DashboardBundleLayout.tsx
import React, { ReactNode } from 'react';

interface DashboardLayoutProps {
  children: ReactNode;
  title: string;
  actions?: ReactNode;
}

export const DashboardBundleLayout: React.FC<DashboardLayoutProps> = ({
  children,
  title,
  actions,
}) => {
  
  return (
    <div className="bg-gray-800"> 
      <div className="py-6">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between items-center mb-6">
            <h1 className="text-2xl font-semibold text-gray-900 dark:text-white">{title}</h1>
            {actions}
          </div>
          <div className="bg-gray-800 overflow-hidden shadow-sm sm:rounded-lg">
            <div className="p-6">
              {children}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

/== templates/DashboardLayout.tsx
import React from 'react';
import Header from '../organisms/Header';

interface DashboardLayoutProps {
  children: React.ReactNode;
  sidebar?: React.ReactNode;
}

const DashboardLayout: React.FC<DashboardLayoutProps> = ({
  children,
  sidebar
}) => {
  return (
    <div className="min-h-screen bg-gray-50">
      <Header />
      
      <div className="flex">
        {sidebar && (
          <aside className="w-64 bg-white border-r border-gray-200 min-h-screen">
            {sidebar}
          </aside>
        )}
        
        <main className="flex-1">
          <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
            {children}
          </div>
        </main>
      </div>
    </div>
  );
};

export default DashboardLayout;

/== templates/MainLayout.tsx
import React from 'react';
import { Outlet } from 'react-router-dom';
import Header from '../organisms/Header';
import Footer from '../organisms/Footer';

const MainLayout: React.FC = () => {
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      <main className="flex-grow">
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}

export default MainLayout;

