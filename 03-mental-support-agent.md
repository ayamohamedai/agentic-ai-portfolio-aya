# 🧠 Prompt Name: Mental Support AI Agentimport React, { useState, useEffect, useRef } from 'react';
import { Heart, MessageCircle, Lightbulb, Wind, Brain, Send, User, Bot, Smile, Frown, Meh, Activity, Clock, Star } from 'lucide-react';

const PsychologicalSupportAgent = () => {
  const [messages, setMessages] = useState([
    {
      id: 1,
      type: 'bot',
      content: 'مرحباً بك 🌟 أنا مساعدك الذكي للدعم النفسي. أنا هنا لأستمع إليك بتعاطف وأساعدك على التعامل مع مشاعرك وضغوط الحياة اليومية. كيف تشعر اليوم؟',
      timestamp: new Date(),
      mood: 'welcoming'
    }
  ]);
  
  const [userInput, setUserInput] = useState('');
  const [isTyping, setIsTyping] = useState(false);
  const [currentMood, setCurrentMood] = useState('neutral');
  const [sessionContext, setSessionContext] = useState({
    topics: [],
    emotions: [],
    suggestions: [],
    sessionDuration: 0
  });
  
  const messagesEndRef = useRef(null);
  const inputRef = useRef(null);

  // قاموس المشاعر والكلمات المفتاحية
  const emotionKeywords = {
    stress: ['ضغط', 'ضغوط', 'متوتر', 'توتر', 'قلق', 'قلقان', 'خوف', 'خايف', 'مضغوط'],
    sadness: ['حزين', 'حزن', 'زعلان', 'مكتئب', 'تعبان', 'يأس', 'إحباط', 'محبط'],
    anger: ['غضب', 'غضبان', 'زعلان', 'متضايق', 'منزعج', 'متعصب', 'مستاء'],
    anxiety: ['قلق', 'قلقان', 'خوف', 'رعب', 'هلع', 'توتر', 'عصبي', 'مضطرب'],
    confusion: ['محتار', 'مش فاهم', 'تشويش', 'ضياع', 'تائه', 'مشوش'],
    happiness: ['سعيد', 'مبسوط', 'فرحان', 'متفائل', 'راضي', 'مرتاح']
  };

  // ردود ذكية حسب المشاعر
  const empathicResponses = {
    stress: [
      'أفهم أنك تمر بضغوط كثيرة الآن. هذا شعور طبيعي تماماً في الحياة. دعنا نتحدث عما يضغط عليك ونجد طرقاً للتعامل معه.',
      'الضغوط جزء من الحياة، لكن المهم هو كيفية التعامل معها. أنا هنا لأساعدك في إيجاد استراتيجيات فعالة للتخفيف من هذا الضغط.'
    ],
    sadness: [
      'أشعر بحزنك، وهذا أمر مفهوم تماماً. المشاعر الحزينة طبيعية وتحتاج وقتاً للمرور. دعنا نتحدث عما يجعلك تشعر بهذا الشكل.',
      'من الطبيعي أن نشعر بالحزن أحياناً. مشاعرك مهمة ومُقَدَّرة. هل تريد أن تحدثني عما يحدث معك؟'
    ],
    anxiety: [
      'القلق شعور صعب، لكنك لست وحدك فيه. يمكننا معاً أن نجد طرقاً لتهدئة عقلك وتقليل هذا القلق. ما الذي يقلقك تحديداً؟',
      'أفهم شعورك بالقلق. دعنا نتنفس معاً ونتحدث عما يدور في رأسك. القلق قابل للإدارة بالتقنيات المناسبة.'
    ],
    anger: [
      'الغضب مشاعر طبيعية، والمهم هو كيفية التعبير عنها بطريقة صحية. دعنا نتحدث عما يغضبك ونجد طرقاً للتعامل مع هذه المشاعر.',
      'أشعر أنك غاضب، وهذا مفهوم. الغضب أحياناً يخبرنا بأن شيئاً مهماً بالنسبة لنا يحتاج انتباه. ما الذي حدث؟'
    ],
    confusion: [
      'الشعور بالحيرة أمر طبيعي عندما نواجه قرارات صعبة أو مواقف معقدة. دعنا نرتب الأفكار معاً خطوة بخطوة.',
      'فهمت أنك تشعر بالتشويش. أحياناً نحتاج فقط لشخص يساعدنا على تنظيم أفكارنا. دعنا نتحدث عما يحيرك.'
    ],
    happiness: [
      'أسعدني أن أراك في حالة جيدة! المشاعر الإيجابية مهمة وتستحق الاحتفال بها. ما الذي يجعلك تشعر بالسعادة اليوم؟',
      'رائع أن تشعر بالسعادة! هذه مشاعر جميلة. هل تريد أن تشاركني ما يجعلك تشعر بهذا الشكل الإيجابي؟'
    ]
  };

  // تقنيات الدعم والتمارين
  const supportTechniques = {
    breathing: {
      title: '🫁 تمرين التنفس العميق',
      description: 'تنفس بعمق لمدة 4 ثوان، احبس النفس لمدة 4 ثوان، ثم اخرج الهواء لمدة 6 ثوان. كرر 5 مرات.',
      duration: '2-3 دقائق'
    },
    meditation: {
      title: '🧘‍♀️ تمرين التأمل البسيط',
      description: 'اجلس بهدوء، أغمض عينيك، وركز على صوت أنفاسك. عندما تشتت أفكارك، أعدها بلطف للتنفس.',
      duration: '5-10 دقائق'
    },
    reframing: {
      title: '🔄 إعادة صياغة الأفكار',
      description: 'حاول النظر للموقف من زاوية مختلفة. اسأل نفسك: "هل هناك طريقة أخرى لفهم هذا الموقف؟"',
      duration: '3-5 دقائق'
    },
    gratitude: {
      title: '🙏 تمرين الامتنان',
      description: 'اكتب أو فكر في 3 أشياء تشعر بالامتنان لها اليوم، مهما كانت صغيرة.',
      duration: '2-3 دقائق'
    },
    grounding: {
      title: '🌍 تمرين الـ 5-4-3-2-1',
      description: 'حدد 5 أشياء تراها، 4 تلمسها، 3 تسمعها، 2 تشمها، وشيء واحد تتذوقه. يساعد على التركيز في اللحظة الحالية.',
      duration: '3-5 دقائق'
    }
  };

  // تحليل المشاعر من النص
  const analyzeEmotion = (text) => {
    const lowerText = text.toLowerCase();
    let detectedEmotion = 'neutral';
    let maxMatches = 0;

    Object.entries(emotionKeywords).forEach(([emotion, keywords]) => {
      const matches = keywords.filter(keyword => lowerText.includes(keyword)).length;
      if (matches > maxMatches) {
        maxMatches = matches;
        detectedEmotion = emotion;
      }
    });

    return detectedEmotion;
  };

  // توليد رد ذكي
  const generateResponse = (userMessage, detectedEmotion) => {
    const responses = empathicResponses[detectedEmotion] || [
      'شكراً لك على مشاركة مشاعرك معي. أنا هنا لأستمع إليك وأساعدك. حدثني أكثر عما تشعر به.',
      'أقدر ثقتك بي في مشاركة مشاعرك. كل ما تشعر به مهم ومُقَدَّر. كيف يمكنني أن أساعدك؟'
    ];
    
    return responses[Math.floor(Math.random() * responses.length)];
  };

  // اقتراح تقنيات حسب المشاعر
  const suggestTechniques = (emotion) => {
    const suggestions = {
      stress: ['breathing', 'meditation', 'reframing'],
      anxiety: ['breathing', 'grounding', 'meditation'],
      sadness: ['gratitude', 'meditation', 'reframing'],
      anger: ['breathing', 'reframing', 'grounding'],
      confusion: ['meditation', 'reframing', 'grounding']
    };
    
    return suggestions[emotion] || ['breathing', 'meditation'];
  };

  // التعامل مع إرسال الرسالة
  const handleSendMessage = async () => {
    if (!userInput.trim()) return;

    const userMessage = {
      id: Date.now(),
      type: 'user',
      content: userInput,
      timestamp: new Date()
    };

    setMessages(prev => [...prev, userMessage]);
    setUserInput('');
    setIsTyping(true);

    // تحليل المشاعر
    const detectedEmotion = analyzeEmotion(userInput);
    setCurrentMood(detectedEmotion);

    // تحديث السياق
    setSessionContext(prev => ({
      ...prev,
      emotions: [...prev.emotions, detectedEmotion],
      topics: [...prev.topics, userInput.slice(0, 50)]
    }));

    // محاكاة وقت الاستجابة
    setTimeout(() => {
      const botResponse = generateResponse(userInput, detectedEmotion);
      const botMessage = {
        id: Date.now() + 1,
        type: 'bot',
        content: botResponse,
        timestamp: new Date(),
        mood: detectedEmotion,
        suggestedTechniques: suggestTechniques(detectedEmotion)
      };

      setMessages(prev => [...prev, botMessage]);
      setIsTyping(false);
    }, 1500);
  };

  // التعامل مع اقتراح التقنيات
  const handleTechniqueClick = (techniqueKey) => {
    const technique = supportTechniques[techniqueKey];
    const techniqueMessage = {
      id: Date.now(),
      type: 'bot',
      content: `${technique.title}\n\n${technique.description}\n\n⏱️ المدة المقترحة: ${technique.duration}\n\nهل تريد أن نجرب هذا التمرين معاً؟`,
      timestamp: new Date(),
      isTechnique: true
    };

    setMessages(prev => [...prev, techniqueMessage]);
  };

  // التمرير للأسفل عند إضافة رسائل جديدة
  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  // حساب مدة الجلسة
  useEffect(() => {
    const interval = setInterval(() => {
      setSessionContext(prev => ({
        ...prev,
        sessionDuration: prev.sessionDuration + 1
      }));
    }, 60000); // كل دقيقة

    return () => clearInterval(interval);
  }, []);

  const getMoodIcon = (mood) => {
    const icons = {
      happiness: <Smile className="w-4 h-4 text-green-500" />,
      sadness: <Frown className="w-4 h-4 text-blue-500" />,
      stress: <Activity className="w-4 h-4 text-red-500" />,
      anxiety: <Activity className="w-4 h-4 text-yellow-500" />,
      anger: <Frown className="w-4 h-4 text-red-600" />,
      neutral: <Meh className="w-4 h-4 text-gray-500" />
    };
    return icons[mood] || icons.neutral;
  };

  const getMoodColor = (mood) => {
    const colors = {
      happiness: 'bg-green-50 border-green-200',
      sadness: 'bg-blue-50 border-blue-200',
      stress: 'bg-red-50 border-red-200',
      anxiety: 'bg-yellow-50 border-yellow-200',
      anger: 'bg-red-50 border-red-300',
      neutral: 'bg-gray-50 border-gray-200',
      welcoming: 'bg-purple-50 border-purple-200'
    };
    return colors[mood] || colors.neutral;
  };

  return (
    <div className="max-w-4xl mx-auto p-6 bg-gradient-to-br from-blue-50 to-purple-50 min-h-screen">
      {/* Header */}
      <div className="bg-white rounded-2xl shadow-lg p-6 mb-6 border border-blue-100">
        <div className="flex items-center justify-between">
          <div className="flex items-center space-x-4">
            <div className="bg-gradient-to-r from-blue-500 to-purple-500 p-3 rounded-full">
              <Brain className="w-8 h-8 text-white" />
            </div>
            <div>
              <h1 className="text-2xl font-bold text-gray-800">وكيل الدعم النفسي الذكي</h1>
              <p className="text-sm text-gray-600">مساعد ذكي للدعم النفسي والعاطفي</p>
            </div>
          </div>
          <div className="flex items-center space-x-4 text-sm text-gray-600">
            <div className="flex items-center space-x-2">
              <Clock className="w-4 h-4" />
              <span>{sessionContext.sessionDuration} دقيقة</span>
            </div>
            <div className="flex items-center space-x-2">
              {getMoodIcon(currentMood)}
              <span className="capitalize">{currentMood}</span>
            </div>
          </div>
        </div>
      </div>

      {/* Chat Container */}
      <div className="bg-white rounded-2xl shadow-lg border border-blue-100 overflow-hidden">
        {/* Messages Area */}
        <div className="h-96 overflow-y-auto p-6 space-y-4 bg-gradient-to-b from-blue-25 to-white">
          {messages.map((message) => (
            <div key={message.id} className={`flex ${message.type === 'user' ? 'justify-end' : 'justify-start'}`}>
              <div className={`flex items-start space-x-3 max-w-xs lg:max-w-md ${message.type === 'user' ? 'flex-row-reverse space-x-reverse' : ''}`}>
                {/* Avatar */}
                <div className={`flex-shrink-0 w-8 h-8 rounded-full flex items-center justify-center ${
                  message.type === 'user' 
                    ? 'bg-blue-500' 
                    : 'bg-gradient-to-r from-purple-400 to-blue-500'
                }`}>
                  {message.type === 'user' ? 
                    <User className="w-4 h-4 text-white" /> : 
                    <Bot className="w-4 h-4 text-white" />
                  }
                </div>
                
                {/* Message Bubble */}
                <div className={`p-4 rounded-2xl shadow-sm border ${
                  message.type === 'user' 
                    ? 'bg-blue-500 text-white border-blue-400' 
                    : `${getMoodColor(message.mood)} text-gray-800`
                }`}>
                  <p className="whitespace-pre-wrap text-sm leading-relaxed">{message.content}</p>
                  <div className="flex items-center justify-between mt-2">
                    <span className={`text-xs ${message.type === 'user' ? 'text-blue-100' : 'text-gray-500'}`}>
                      {message.timestamp.toLocaleTimeString('ar-EG', { 
                        hour: '2-digit', 
                        minute: '2-digit',
                        hour12: false 
                      })}
                    </span>
                  </div>
                  
                  {/* Suggested Techniques */}
                  {message.suggestedTechniques && (
                    <div className="mt-4 space-y-2">
                      <p className="text-sm font-medium text-gray-700 mb-2">💡 تقنيات مقترحة للمساعدة:</p>
                      <div className="space-y-1">
                        {message.suggestedTechniques.map((techKey) => (
                          <button
                            key={techKey}
                            onClick={() => handleTechniqueClick(techKey)}
                            className="block w-full text-left p-2 rounded-lg bg-white hover:bg-blue-50 border border-blue-100 hover:border-blue-200 transition-colors text-sm"
                          >
                            {supportTechniques[techKey].title}
                          </button>
                        ))}
                      </div>
                    </div>
                  )}
                </div>
              </div>
            </div>
          ))}
          
          {/* Typing Indicator */}
          {isTyping && (
            <div className="flex justify-start">
              <div className="flex items-start space-x-3">
                <div className="flex-shrink-0 w-8 h-8 bg-gradient-to-r from-purple-400 to-blue-500 rounded-full flex items-center justify-center">
                  <Bot className="w-4 h-4 text-white" />
                </div>
                <div className="bg-gray-100 border border-gray-200 p-4 rounded-2xl">
                  <div className="flex space-x-1">
                    <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce"></div>
                    <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style={{animationDelay: '0.1s'}}></div>
                    <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style={{animationDelay: '0.2s'}}></div>
                  </div>
                </div>
              </div>
            </div>
          )}
          
          <div ref={messagesEndRef} />
        </div>

        {/* Input Area */}
        <div className="p-6 bg-gray-50 border-t border-gray-100">
          <div className="flex space-x-4">
            <input
              ref={inputRef}
              type="text"
              value={userInput}
              onChange={(e) => setUserInput(e.target.value)}
              onKeyPress={(e) => e.key === 'Enter' && handleSendMessage()}
              placeholder="اكتب مشاعرك أو ما يشغل بالك..."
              className="flex-1 p-4 border border-gray-200 rounded-2xl focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-transparent text-sm"
              disabled={isTyping}
            />
            <button
              onClick={handleSendMessage}
              disabled={isTyping || !userInput.trim()}
              className="bg-gradient-to-r from-blue-500 to-purple-500 text-white p-4 rounded-2xl hover:from-blue-600 hover:to-purple-600 transition-colors disabled:opacity-50 disabled:cursor-not-allowed shadow-lg"
            >
              <Send className="w-5 h-5" />
            </button>
          </div>
          
          {/* Quick Actions */}
          <div className="mt-4 flex flex-wrap gap-2">
            {Object.entries(supportTechniques).slice(0, 3).map(([key, technique]) => (
              <button
                key={key}
                onClick={() => handleTechniqueClick(key)}
                className="px-3 py-1 bg-white border border-blue-200 text-blue-600 rounded-full text-xs hover:bg-blue-50 transition-colors flex items-center space-x-1"
              >
                <span>{technique.title.split(' ')[0]}</span>
                <Lightbulb className="w-3 h-3" />
              </button>
            ))}
          </div>
        </div>
      </div>

      {/* Footer */}
      <div className="mt-6 text-center">
        <div className="bg-white rounded-lg p-4 border border-gray-100 shadow-sm">
          <p className="text-xs text-gray-600 mb-2">
            ⚖️ <strong>إخلاء مسؤولية:</strong> هذا المساعد لا يُعد بديلاً للعلاج النفسي أو الطبي المتخصص
          </p>
          <p className="text-xs text-gray-500">
            جميع الحقوق محفوظة لـ <strong>لايه محمد</strong> © {new Date().getFullYear()}
          </p>
        </div>
      </div>
    </div>
  );
};

export default PsychologicalSupportAgent;