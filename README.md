const filters = {
  none: { brightness: 100, contrast: 110, saturate: 100, sepia: 0, hue: 0 },
  cinematic: { brightness: 94, contrast: 128, saturate: 90, sepia: 0.2, hue: -8 },
  warm: { brightness: 110, contrast: 118, saturate: 123, sepia: 0.2, hue: 16 },
  cool: { brightness: 96, contrast: 118, saturate: 80, sepia: 0.05, hue: -22 },
  vintage: { brightness: 106, contrast: 118, saturate: 70, sepia: 0.42, hue: 14 }
};

const preview = document.getElementById('videoSurface');
const captionBubble = document.getElementById('captionBubble');
const captionInput = document.getElementById('captionInput');
const applyCaptionBtn = document.getElementById('applyCaptionBtn');
const playPauseBtn = document.getElementById('playPauseBtn');
const timeBar = document.getElementById('timeBar');
const timeReadout = document.getElementById('timeReadout');
const timeline = document.getElementById('timeline');

const brightnessRange = document.getElementById('brightnessRange');
const contrastRange = document.getElementById('contrastRange');
const saturationRange = document.getElementById('saturationRange');

const brightnessValue = document.getElementById('brightnessValue');
const contrastValue = document.getElementById('contrastValue');
const saturationValue = document.getElementById('saturationValue');

const clips = [
  { name: 'Abertura', start: '00:00', duration: '00:10' },
  { name: 'Cena do produto', start: '00:10', duration: '00:12' },
  { name: 'CTA final', start: '00:22', duration: '00:13' }
];

let playbackValue = 32;
let isPlaying = false;
let activeFilter = 'none';

function renderTimeline() {
  timeline.innerHTML = '';

  clips.forEach((clip, index) => {
    const item = document.createElement('div');
    item.className = 'track-item';

    const content = document.createElement('div');
    content.style.display = 'flex';
    content.style.alignItems = 'center';
    content.style.gap = '12px';
    content.style.flex = '1';

    const label = document.createElement('div');
    label.innerHTML = `<strong>${clip.name}</strong><br><span>${clip.start} • ${clip.duration}</span>`;

    const meter = document.createElement('div');
    meter.className = 'track-bar';
    const fill = document.createElement('div');
    fill.className = 'track-fill';
    fill.style.width = `${55 + index * 15}%`;
    meter.appendChild(fill);

    content.appendChild(label);
    item.appendChild(content);
    item.appendChild(meter);
    timeline.appendChild(item);
  });
}

function updateTimeReadout() {
  const currentMinutes = Math.floor(playbackValue / 100 * 35);
  const currentSeconds = Math.floor((playbackValue / 100 * 35 - currentMinutes) * 60);
  const displayedCurrent = `${String(currentMinutes).padStart(2, '0')}:${String(currentSeconds).padStart(2, '0')}`;
  timeReadout.textContent = `${displayedCurrent} / 00:35`;
}

function applyFilterStyles() {
  const filterSet = filters[activeFilter] || filters.none;
  const brightness = brightnessRange.value;
  const contrast = contrastRange.value;
  const saturation = saturationRange.value;

  preview.style.filter = `brightness(${brightness}%) contrast(${contrast}%) saturate(${saturation}%) sepia(${filterSet.sepia}) hue-rotate(${filterSet.hue}deg)`;
  brightnessValue.textContent = `${brightness}%`;
  contrastValue.textContent = `${contrast}%`;
  saturationValue.textContent = `${saturation}%`;
}

function setFilter(filterName) {
  activeFilter = filterName;
  document.querySelectorAll('.filter-btn').forEach((button) => {
    button.classList.toggle('active', button.dataset.filter === filterName);
  });
  applyFilterStyles();
}

function updatePlayback() {
  if (isPlaying) {
    playbackValue = (playbackValue + 1) % 100;
    timeBar.value = playbackValue;
    updateTimeReadout();

    if (playbackValue >= 98) {
      isPlaying = false;
      playPauseBtn.textContent = '▶';
    }
  }
}

function togglePlayback() {
  isPlaying = !isPlaying;
  playPauseBtn.textContent = isPlaying ? '❚❚' : '▶';
}

brightnessRange.addEventListener('input', () => {
  applyFilterStyles();
});

contrastRange.addEventListener('input', () => {
  applyFilterStyles();
});

saturationRange.addEventListener('input', () => {
  applyFilterStyles();
});

applyCaptionBtn.addEventListener('click', () => {
  captionBubble.textContent = captionInput.value.trim() || 'A ação começa agora';
});

document.querySelectorAll('.filter-btn').forEach((button) => {
  button.addEventListener('click', () => setFilter(button.dataset.filter));
});

playPauseBtn.addEventListener('click', togglePlayback);

timeBar.addEventListener('input', (event) => {
  playbackValue = Number(event.target.value);
  updateTimeReadout();
});

document.querySelectorAll('.tool-btn').forEach((button) => {
  button.addEventListener('click', () => {
    document.querySelectorAll('.tool-btn').forEach((btn) => btn.classList.remove('active'));
    button.classList.add('active');
  });
});

setFilter('none');
renderTimeline();
updateTimeReadout();
setInterval(updatePlayback, 110);
