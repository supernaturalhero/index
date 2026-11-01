const body = document.body;
const themeToggle = document.querySelector('.theme-toggle');
const revealItems = document.querySelectorAll('.reveal');
const counters = document.querySelectorAll('[data-counter]');
const typewriterEl = document.querySelector('.typewriter');

const root = document.documentElement;

const storedTheme = localStorage.getItem('theme');
if (storedTheme === 'light') {
  body.classList.add('light-theme');
}

const setTheme = (theme) => {
  body.classList.toggle('light-theme', theme === 'light');
  localStorage.setItem('theme', theme);
};

themeToggle.addEventListener('click', () => {
  const nextTheme = body.classList.contains('light-theme') ? 'dark' : 'light';
  setTheme(nextTheme);
});

const revealObserver = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        revealObserver.unobserve(entry.target);
      }
    });
  },
  { threshold: 0.18 }
);

revealItems.forEach((item) => revealObserver.observe(item));

const animateCounter = (el) => {
  const target = Number(el.dataset.counter);
  const duration = 1200;
  const start = performance.now();

  const tick = (time) => {
    const progress = Math.min((time - start) / duration, 1);
    const eased = 1 - Math.pow(1 - progress, 3);
    el.textContent = Math.round(target * eased);

    if (progress < 1) {
      requestAnimationFrame(tick);
    } else {
      el.textContent = target;
    }
  };

  requestAnimationFrame(tick);
};

const counterObserver = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        animateCounter(entry.target);
        counterObserver.unobserve(entry.target);
      }
    });
  },
  { threshold: 0.6 }
);

counters.forEach((counter) => counterObserver.observe(counter));

const text = typewriterEl.dataset.text;
let index = 0;

const typeText = () => {
  if (!typewriterEl) return;

  typewriterEl.textContent = text.slice(0, index);
  index += 1;

  if (index <= text.length) {
    setTimeout(typeText, 60);
  } else {
    setTimeout(() => {
      index = 0;
      typeText();
    }, 1800);
  }
};

typeText();

const projectCards = document.querySelectorAll('.project-card');
projectCards.forEach((card) => {
  card.addEventListener('mousemove', (e) => {
    const rect = card.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    const rotateY = ((x / rect.width) - 0.5) * 12;
    const rotateX = (0.5 - (y / rect.height)) * 12;

    card.style.transform = `perspective(1000px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) translateY(-6px)`;
  });

  card.addEventListener('mouseleave', () => {
    card.style.transform = 'translateY(0)';
  });
});
