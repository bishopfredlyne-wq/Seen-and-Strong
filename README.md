
import React, { useMemo, useState } from 'react';

const feelings = [
  'Sad',
  'Anxious',
  'Angry',
  'Overwhelmed',
  'Lonely',
  'Tired',
  'Hopeless',
  'Just need encouragement',
];

const ageRanges = ['Under 18', '18–25', '26–40', '41–60', '60+'];
const moodOptions = ['😊 Happy', '🙂 Okay', '😐 Neutral', '😔 Sad', '😞 Struggling'];

const strengthWords = [
  {
    word: 'Courage',
    text: 'Courage is not the absence of fear. It is choosing to keep going even when your heart feels heavy.',
  },
  {
    word: 'Hope',
    text: 'Hope is the quiet light that stays alive, even when the day feels dark.',
  },
  {
    word: 'Peace',
    text: 'Peace begins in one pause, one breath, and one kind moment with yourself.',
  },
  {
    word: 'Strength',
    text: 'Strength is not always loud. Sometimes it is simply staying, breathing, and trying again.',
  },
  {
    word: 'Healing',
    text: 'Healing is not a race. Every gentle step forward still counts.',
  },
];

const library = {
  Anxiety: 'You do not have to solve everything today. Let this moment be small enough to breathe through.',
  Loneliness: 'Even when connection feels far away, your life still holds meaning and your presence still matters.',
  'Self-worth': 'You are worthy of care, gentleness, and respect — even on the days that feel heavy.',
  Healing: 'Healing often begins quietly, with one honest breath and one kind decision toward yourself.',
  Confidence: 'You do not need to feel fearless to take the next step. Small courage still counts.',
  Strength: 'You have already survived moments you once thought would break you. That strength is still within you.',
  'Starting again': 'Beginning again is not failure. It is courage choosing not to give up.',
};

const smallSteps = [
  'Let’s pause together for a moment. Take 3 slow breaths — in… and out…',
  'Drink a glass of water slowly and let your body receive care.',
  'Step outside for fresh air and allow yourself one gentle minute of quiet.',
  'Write one sentence about how you feel. It does not have to be perfect.',
  'Text someone you trust and simply say, “I need a little encouragement today.”',
  'Stretch your body gently for 2 minutes and release some of the tension you are carrying.',
  'Listen to calming music for a few minutes and let your mind soften.',
];

const affirmations = [
  (name) => `${name}, you are not defined by your hardest days. You are still standing, still learning, and still worthy of love.`,
  (name) => `${name}, even in this moment, there is still strength in you — even if it feels quiet right now.`,
  (name) => `${name}, your life still carries meaning. This moment matters, but it does not define your whole story.`,
];

function containsCrisisLanguage(text) {
  const value = (text || '').toLowerCase();
  return [
    'i want to die',
    'i want to hurt myself',
    'kill myself',
    'end my life',
    'suicide',
    'self harm',
  ].some((phrase) => value.includes(phrase));
}

function messageFor({ name, feeling, challenge, faithMode }) {
  const user = name?.trim() || 'Friend';

  const intros = {
    Sad: `${user}, thank you for sharing this with me. I’m really glad you didn’t keep it inside. Sadness can feel heavy, like everything is slower and harder than it should be.`,
    Anxious: `${user}, I’m really glad you came here. Anxiety can make everything feel urgent and overwhelming, like you have to fix your whole life at once.`,
    Angry: `${user}, it’s okay to admit that you feel angry. Strong emotions often rise from deep hurt, disappointment, or exhaustion.`,
    Overwhelmed: `${user}, I can feel how much you’re carrying right now. When everything comes at once, it can feel like too much.`,
    Lonely: `${user}, I hear you. Loneliness can feel very deep — like you are surrounded by life, but still unseen.`,
    Tired: `${user}, it sounds like you’ve been carrying a lot for a long time. This kind of tiredness is not just physical — it is deep.`,
    Hopeless: `${user}, I’m really grateful you came here. I know it’s not easy to reach out when everything feels heavy.`,
    'Just need encouragement': `${user}, I’m really glad you came here today. Reaching out for encouragement takes strength — even if it does not feel like it.`,
  };

  const worth = {
    Sad: 'But nothing about this feeling takes away who you are. You are still valuable. Still needed. Still enough.',
    Anxious: 'But you do not have to solve everything right now. You are allowed to pause. You are allowed to breathe.',
    Angry: 'Take a moment before reacting, not to ignore your feelings, but to protect your peace.',
    Overwhelmed: 'Let’s make things smaller. Just this moment. Just this breath. You do not have to solve everything today.',
    Lonely: 'But your presence still matters more than you know. You are not invisible. You are not forgotten.',
    Tired: 'You do not have to push through everything today. Rest is not weakness. Rest is part of healing.',
    Hopeless: 'This moment is not the end of your story. Something in you is still here, still breathing, still trying.',
    'Just need encouragement': 'You are still showing up, still trying, and still moving forward in your own way. And that matters.',
  };

  const endings = [
    'You are not invisible.',
    'You matter — even now.',
    'This moment is not your whole story.',
    'You are still becoming.',
  ];

  const challengeLine = challenge?.trim()
    ? ` What you shared matters: “${challenge.trim()}”.`
    : '';

  const ending = endings[(user.length + (challenge?.length || 0)) % endings.length];
  const faithLine = faithMode
    ? ' Even when you cannot see the road ahead, your life still carries purpose.'
    : '';

  return `${intros[feeling] || intros['Just need encouragement']}${challengeLine} ${worth[feeling] || worth['Just need encouragement']} Even now, ${user}, there is still strength in you — even if it feels small right now.${faithLine} ${ending}`;
}

function AppCard({ children }) {
  return (
    <div className="rounded-[28px] bg-white p-6 shadow-[0_10px_30px_rgba(15,23,42,0.08)] sm:p-8">
      {children}
    </div>
  );
}

function Button({ children, onClick, secondary = false, className = '', disabled = false }) {
  return (
    <button
      disabled={disabled}
      onClick={onClick}
      className={`rounded-2xl px-5 py-3 text-sm font-semibold transition ${secondary ? 'border border-slate-300 bg-white text-slate-700 hover:bg-slate-50' : 'bg-slate-800 text-white hover:bg-slate-700'} ${disabled ? 'cursor-not-allowed opacity-50' : ''} ${className}`}
    >
      {children}
    </button>
  );
}

export default function SeenAndStrongPrototype() {
  const [screen, setScreen] = useState('welcome');
  const [name, setName] = useState('');
  const [feeling, setFeeling] = useState('');
  const [challenge, setChallenge] = useState('');
  const [ageRange, setAgeRange] = useState('');
  const [faithMode, setFaithMode] = useState(false);
  const [moodHistory, setMoodHistory] = useState([]);
  const [messageCount, setMessageCount] = useState(0);

  const strength = useMemo(() => strengthWords[new Date().getDate() % strengthWords.length], []);
  const encouragement = useMemo(() => messageFor({ name, feeling, challenge, faithMode }), [name, feeling, challenge, faithMode, messageCount]);
  const smallStep = useMemo(() => smallSteps[(name.length + challenge.length + messageCount) % smallSteps.length], [name, challenge, messageCount]);
  const personalAffirmation = useMemo(() => affirmations[(name.length + 1) % affirmations.length](name || 'Friend'), [name]);

  const handleEncourage = () => {
    if (containsCrisisLanguage(challenge)) {
      setScreen('emergency');
      return;
    }
    if (!feeling) return;
    setScreen('response');
  };

  const addMood = (mood) => {
    setMoodHistory((prev) => [mood, ...prev].slice(0, 7));
  };

  const readAloud = () => {
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(`${encouragement} Small step for today. ${smallStep}`);
      utterance.rate = 0.92;
      window.speechSynthesis.cancel();
      window.speechSynthesis.speak(utterance);
    }
  };

  return (
    <div className="min-h-screen bg-slate-50 px-4 py-6 text-slate-800 sm:px-6 lg:px-8">
      <div className="mx-auto max-w-5xl space-y-6">
        <div className="grid gap-4 lg:grid-cols-[1.7fr,1fr]">
          <AppCard>
            <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
              <div>
                <h1 className="text-3xl font-bold tracking-tight">Seen &amp; Strong</h1>
                <p className="mt-2 max-w-2xl text-sm leading-6 text-slate-600">
                  You are seen. You are heard. You are stronger than this moment.
                </p>
              </div>
              <label className="flex items-center gap-3 rounded-full bg-slate-100 px-4 py-2 text-sm text-slate-700">
                <span>Faith Mode</span>
                <input type="checkbox" checked={faithMode} onChange={(e) => setFaithMode(e.target.checked)} className="h-4 w-4" />
              </label>
            </div>
          </AppCard>

          <AppCard>
            <p className="text-xs font-semibold uppercase tracking-[0.22em] text-slate-500">Today’s Strength Word</p>
            <h2 className="mt-2 text-2xl font-bold">{strength.word}</h2>
            <p className="mt-3 text-sm leading-6 text-slate-600">{strength.text}</p>
          </AppCard>
        </div>

        <div className="grid gap-6 lg:grid-cols-[1.5fr,1fr]">
          {screen === 'welcome' && (
            <AppCard>
              <div className="space-y-6 text-center">
                <div className="mx-auto flex h-20 w-20 items-center justify-center rounded-full bg-slate-100 text-3xl">💙</div>
                <div>
                  <h2 className="text-3xl font-bold">Welcome to Seen &amp; Strong</h2>
                  <p className="mx-auto mt-4 max-w-2xl text-base leading-8 text-slate-600">
                    This is a safe place for encouragement and hope. You are not invisible. You are not alone.
                    Take a moment and share what you are going through.
                  </p>
                </div>
                <Button onClick={() => setScreen('input')} className="w-full sm:w-auto">Start</Button>
              </div>
            </AppCard>
          )}

          {screen === 'input' && (
            <AppCard>
              <div className="space-y-5">
                <div className="flex items-center justify-between gap-4">
                  <h2 className="text-2xl font-bold">Share how you feel</h2>
                  <Button secondary onClick={() => setScreen('welcome')}>Back</Button>
                </div>

                <div className="grid gap-4 md:grid-cols-2">
                  <div>
                    <label className="mb-2 block text-sm font-medium text-slate-700">Name</label>
                    <input
                      value={name}
                      onChange={(e) => setName(e.target.value)}
                      placeholder="Michael"
                      className="w-full rounded-2xl border border-slate-200 px-4 py-3 outline-none focus:border-slate-400"
                    />
                  </div>
                  <div>
                    <label className="mb-2 block text-sm font-medium text-slate-700">Optional: Age range</label>
                    <select
                      value={ageRange}
                      onChange={(e) => setAgeRange(e.target.value)}
                      className="w-full rounded-2xl border border-slate-200 px-4 py-3 outline-none focus:border-slate-400"
                    >
                      <option value="">Choose age range</option>
                      {ageRanges.map((item) => <option key={item}>{item}</option>)}
                    </select>
                  </div>
                </div>

                <div>
                  <label className="mb-2 block text-sm font-medium text-slate-700">How do you feel today?</label>
                  <select
                    value={feeling}
                    onChange={(e) => setFeeling(e.target.value)}
                    className="w-full rounded-2xl border border-slate-200 px-4 py-3 outline-none focus:border-slate-400"
                  >
                    <option value="">Select a feeling</option>
                    {feelings.map((item) => <option key={item}>{item}</option>)}
                  </select>
                </div>

                <div>
                  <label className="mb-2 block text-sm font-medium text-slate-700">What challenge are you facing today?</label>
                  <textarea
                    value={challenge}
                    onChange={(e) => setChallenge(e.target.value)}
                    placeholder="I feel like I keep failing in life."
                    className="min-h-[140px] w-full rounded-2xl border border-slate-200 px-4 py-3 outline-none focus:border-slate-400"
                  />
                </div>

                <Button onClick={handleEncourage} disabled={!feeling} className="w-full">Encourage Me</Button>
              </div>
            </AppCard>
          )}

          {screen === 'response' && (
            <AppCard>
              <div className="space-y-5">
                <div className="flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
                  <h2 className="text-2xl font-bold">Your encouragement</h2>
                  <div className="flex gap-3">
                    <Button secondary onClick={() => setScreen('input')}>Back</Button>
                    <Button secondary onClick={readAloud}>Read Message Aloud</Button>
                  </div>
                </div>

                <div className="rounded-3xl bg-slate-50 p-5">
                  <p className="text-base leading-8 text-slate-700">{encouragement}</p>
                </div>

                <div className="rounded-3xl bg-slate-50 p-5">
                  <p className="text-xs font-semibold uppercase tracking-[0.22em] text-slate-500">Small Step for Today</p>
                  <p className="mt-3 text-base leading-7 text-slate-700">{smallStep}</p>
                </div>

                <div className="grid gap-3 sm:grid-cols-2">
                  <Button onClick={() => setMessageCount((v) => v + 1)}>Another Message</Button>
                  <Button secondary onClick={() => setScreen('library')}>Encouragement Library</Button>
                </div>
              </div>
            </AppCard>
          )}

          {screen === 'library' && (
            <AppCard>
              <div className="space-y-5">
                <div className="flex items-center justify-between gap-4">
                  <h2 className="text-2xl font-bold">Encouragement Library</h2>
                  <Button secondary onClick={() => setScreen('response')}>Back</Button>
                </div>
                <div className="grid gap-4 sm:grid-cols-2">
                  {Object.entries(library).map(([title, text]) => (
                    <div key={title} className="rounded-3xl border border-slate-200 p-5">
                      <h3 className="text-lg font-semibold">{title}</h3>
                      <p className="mt-2 text-sm leading-6 text-slate-600">{text}</p>
                    </div>
                  ))}
                </div>
              </div>
            </AppCard>
          )}

          {screen === 'emergency' && (
            <AppCard>
              <div className="space-y-5">
                <h2 className="text-2xl font-bold">Please reach out for immediate support</h2>
                <p className="text-base leading-8 text-slate-700">
                  I’m really sorry that you are hurting this deeply. You deserve immediate support from a real person who can help you through this moment. Please contact a trusted support service now.
                </p>
                <div className="grid gap-3 sm:grid-cols-3">
                  <div className="rounded-3xl bg-slate-50 p-4"><strong>Samaritans</strong><br />116 123</div>
                  <div className="rounded-3xl bg-slate-50 p-4"><strong>NHS 111</strong><br />111</div>
                  <div className="rounded-3xl bg-slate-50 p-4"><strong>Emergency services</strong><br />999</div>
                </div>
                <Button onClick={() => setScreen('welcome')}>Return to Welcome</Button>
              </div>
            </AppCard>
          )}

          <div className="space-y-6">
            <AppCard>
              <p className="text-xs font-semibold uppercase tracking-[0.22em] text-slate-500">Mood Tracker</p>
              <div className="mt-4 grid grid-cols-1 gap-2">
                {moodOptions.map((mood) => (
                  <button
                    key={mood}
                    onClick={() => addMood(mood)}
                    className="rounded-2xl border border-slate-200 px-4 py-3 text-left text-sm hover:bg-slate-50"
                  >
                    {mood}
                  </button>
                ))}
              </div>
              <p className="mt-4 text-sm leading-6 text-slate-600">
                {moodHistory.length > 0
                  ? `You checked in ${moodHistory.length} time${moodHistory.length > 1 ? 's' : ''} this week. That shows commitment to your wellbeing.`
                  : 'Choose a mood to begin your weekly check-in.'}
              </p>
            </AppCard>

            <AppCard>
              <p className="text-xs font-semibold uppercase tracking-[0.22em] text-slate-500">Personal Affirmation</p>
              <p className="mt-3 text-base leading-7 text-slate-700">{personalAffirmation}</p>
            </AppCard>

            <AppCard>
              <p className="text-xs font-semibold uppercase tracking-[0.22em] text-slate-500">Gentle Reminder</p>
              <p className="mt-3 text-sm leading-7 text-slate-600">Seen &amp; Strong provides encouragement and emotional support. It is not a medical or therapy service.</p>
            </AppCard>
          </div>
        </div>
      </div>
    </div>
  );
}

