[shifter.html](https://github.com/user-attachments/files/29354612/shifter.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Parallel Filter & Pitch Music Player</title>
    <style>
        /* Black Theme Styles */
        body {
            background-color: #0a0a0a;
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .player-container {
            background-color: #161616;
            border: 1px solid #333;
            border-radius: 12px;
            padding: 30px;
            width: 100%;
            max-width: 450px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.8);
        }

        h2 {
            margin-top: 0;
            text-align: center;
            font-weight: 400;
            letter-spacing: 1px;
            color: #e0e0e0;
        }

        .upload-section {
            margin-bottom: 20px;
            text-align: center;
        }

        input[type="file"] {
            background-color: #222;
            color: #aaa;
            padding: 10px;
            border-radius: 6px;
            border: 1px solid #444;
            width: 90%;
            cursor: pointer;
        }

        audio {
            width: 100%;
            margin-bottom: 25px;
            outline: none;
        }

        /* Filter Controls */
        .filter-control {
            margin-bottom: 20px;
            background: #0f0f0f;
            padding: 15px;
            border-radius: 8px;
            border: 1px solid #2a2a2a;
        }

        .filter-title {
            font-size: 1.1em;
            color: #fff;
            margin-bottom: 15px;
            border-bottom: 1px solid #333;
            padding-bottom: 5px;
        }

        .slider-group {
            margin-bottom: 15px;
        }

        .slider-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            font-size: 0.85em;
            color: #bbb;
        }

        .value-display {
            font-weight: bold;
        }

        .lp-color { color: #00e676; } /* Green for Low Pass */
        .hp-color { color: #00b0ff; } /* Blue for High Pass */
        .pitch-color { color: #d500f9; } /* Purple for Pitch Shifter */

        /* Custom Range Sliders */
        input[type="range"] {
            width: 100%;
            cursor: pointer;
        }

        .lp-slider { accent-color: #00e676; }
        .hp-slider { accent-color: #00b0ff; }
        .pitch-slider { accent-color: #d500f9; }

        /* Preset Manager Styling */
        .preset-section {
            margin-top: 25px;
            background: #0f0f0f;
            padding: 15px;
            border-radius: 8px;
            border: 1px solid #2a2a2a;
        }

        .preset-row {
            display: flex;
            gap: 10px;
            margin-bottom: 12px;
        }

        #preset-select {
            flex: 1;
            background-color: #222;
            color: #fff;
            border: 1px solid #444;
            padding: 8px;
            border-radius: 6px;
            outline: none;
            cursor: pointer;
        }

        #delete-preset-btn {
            background-color: #cf6679;
            color: #000;
            border: none;
            padding: 8px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
        }

        #delete-preset-btn:hover {
            background-color: #ff4a68;
        }

        #preset-name {
            flex: 1;
            background-color: #222;
            color: #fff;
            border: 1px solid #444;
            padding: 8px;
            border-radius: 6px;
            outline: none;
        }

        #save-preset-btn {
            background-color: #d500f9;
            color: #fff;
            border: none;
            padding: 8px 16px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
        }

        #save-preset-btn:hover {
            background-color: #aa00ff;
        }

        .instructions {
            font-size: 0.8em;
            color: #666;
            text-align: center;
            margin-top: 15px;
            line-height: 1.4;
        }
    </style>
</head>
<body>

    <div class="player-container">
        <h2>Parallel Filter Mixer</h2>
        
        <div class="upload-section">
            <input type="file" id="audio-upload" accept="audio/*">
        </div>

        <audio id="audio-element" controls></audio>

        <div class="filter-control">
            <div class="filter-title lp-color">Feed 1: Low Pass</div>
            
            <div class="slider-group">
                <div class="slider-header">
                    <span>Frequency Cutoff</span>
                    <span class="value-display lp-color"><span id="lp-val">1000</span> Hz</span>
                </div>
                <input type="range" class="lp-slider" id="lp-slider" min="20" max="20000" value="1000" step="10">
            </div>

            <div class="slider-group" style="margin-bottom: 0;">
                <div class="slider-header">
                    <span>Feed Volume</span>
                    <span class="value-display lp-color"><span id="lp-vol-val">100</span>%</span>
                </div>
                <input type="range" class="lp-slider" id="lp-vol-slider" min="0" max="200" value="100" step="1">
            </div>
        </div>

        <div class="filter-control">
            <div class="filter-title hp-color">Feed 2: High Pass</div>
            
            <div class="slider-group">
                <div class="slider-header">
                    <span>Frequency Cutoff</span>
                    <span class="value-display hp-color"><span id="hp-val">1000</span> Hz</span>
                </div>
                <input type="range" class="hp-slider" id="hp-slider" min="20" max="20000" value="1000" step="10">
            </div>

            <div class="slider-group" style="margin-bottom: 0;">
                <div class="slider-header">
                    <span>Feed Volume</span>
                    <span class="value-display hp-color"><span id="hp-vol-val">100</span>%</span>
                </div>
                <input type="range" class="hp-slider" id="hp-vol-slider" min="0" max="200" value="100" step="1">
            </div>
        </div>

        <div class="filter-control">
            <div class="filter-title pitch-color">Global Pitch Shifter (Tape Style)</div>
            
            <div class="slider-group" style="margin-bottom: 0;">
                <div class="slider-header">
                    <span>Pitch Speed Factor</span>
                    <span class="value-display pitch-color"><span id="pitch-val">1.00</span>x</span>
                </div>
                <input type="range" class="pitch-slider" id="pitch-slider" min="0.5" max="2.0" value="1.0" step="0.05">
            </div>
        </div>

        <div class="preset-section">
            <div class="filter-title" style="border: none; margin-bottom: 10px;">Mix Presets</div>
            <div class="preset-row">
                <select id="preset-select">
                    <option value="">-- Load a Preset --</option>
                </select>
                <button id="delete-preset-btn" title="Delete selected preset">✕</button>
            </div>
            <div class="preset-row">
                <input type="text" id="preset-name" placeholder="New Preset Name...">
                <button id="save-preset-btn">Save</button>
            </div>
        </div>

        <div class="instructions">
            The audio is split into two simultaneous copies. Use the volume sliders to mix your filters, tweak the global tape pitch, and save your presets locally!
        </div>
    </div>

    <script>
        const audioInput = document.getElementById('audio-upload');
        const audioElement = document.getElementById('audio-element');
        
        // Sliders & Value Displays
        const lpSlider = document.getElementById('lp-slider');
        const lpVolSlider = document.getElementById('lp-vol-slider');
        const lpVal = document.getElementById('lp-val');
        const lpVolVal = document.getElementById('lp-vol-val');

        const hpSlider = document.getElementById('hp-slider');
        const hpVolSlider = document.getElementById('hp-vol-slider');
        const hpVal = document.getElementById('hp-val');
        const hpVolVal = document.getElementById('hp-vol-val');

        const pitchSlider = document.getElementById('pitch-slider');
        const pitchVal = document.getElementById('pitch-val');

        // Preset Elements
        const presetSelect = document.getElementById('preset-select');
        const presetNameInput = document.getElementById('preset-name');
        const savePresetBtn = document.getElementById('save-preset-btn');
        const deletePresetBtn = document.getElementById('delete-preset-btn');

        // Storage Key
        const STORAGE_KEY = 'parallel_filter_mixer_presets';

        // Global Audio Node References for live updates
        let audioCtx;
        let isInitialized = false;
        let lowPassFilter, lowPassGain, highPassFilter, highPassGain;

        // Initialize Native Pitch Rules
        audioElement.preservesPitch = false;

        function initAudio() {
            if (isInitialized) return;
            
            audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            const track = audioCtx.createMediaElementSource(audioElement);

            // --- Feed 1: Low Pass Path ---
            lowPassFilter = audioCtx.createBiquadFilter();
            lowPassFilter.type = 'lowpass';
            lowPassFilter.frequency.value = lpSlider.value;

            lowPassGain = audioCtx.createGain();
            lowPassGain.gain.value = lpVolSlider.value / 100;

            // --- Feed 2: High Pass Path ---
            highPassFilter = audioCtx.createBiquadFilter();
            highPassFilter.type = 'highpass';
            highPassFilter.frequency.value = hpSlider.value;

            highPassGain = audioCtx.createGain();
            highPassGain.gain.value = hpVolSlider.value / 100;

            // --- PARALLEL ROUTING ---
            track.connect(lowPassFilter);
            track.connect(highPassFilter);

            lowPassFilter.connect(lowPassGain);
            highPassFilter.connect(highPassGain);

            lowPassGain.connect(audioCtx.destination);
            highPassGain.connect(audioCtx.destination);

            isInitialized = true;
        }

        // --- Live Interaction Event Listeners ---
        lpSlider.addEventListener('input', () => {
            if (isInitialized) lowPassFilter.frequency.value = lpSlider.value;
            lpVal.textContent = lpSlider.value;
        });

        lpVolSlider.addEventListener('input', () => {
            if (isInitialized) lowPassGain.gain.value = lpVolSlider.value / 100;
            lpVolVal.textContent = lpVolSlider.value;
        });

        hpSlider.addEventListener('input', () => {
            if (isInitialized) highPassFilter.frequency.value = hpSlider.value;
            hpVal.textContent = hpSlider.value;
        });

        hpVolSlider.addEventListener('input', () => {
            if (isInitialized) highPassGain.gain.value = hpVolSlider.value / 100;
            hpVolVal.textContent = hpVolSlider.value;
        });

        pitchSlider.addEventListener('input', () => {
            const pitchValue = pitchSlider.value;
            audioElement.playbackRate = pitchValue;
            pitchVal.textContent = parseFloat(pitchValue).toFixed(2);
        });

        // Initialize on play to satisfy browser autoplay rules
        audioElement.addEventListener('play', () => {
            initAudio();
            audioElement.preservesPitch = false; 
            audioElement.playbackRate = pitchSlider.value; // sync pitch position on play
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        });

        // Handle local file uploads
        audioInput.addEventListener('change', function() {
            const files = this.files;
            if (files.length > 0) {
                const fileURL = URL.createObjectURL(files[0]);
                audioElement.src = fileURL;
                audioElement.load();
                // Ensure browser settings don't override pitch preferences on source change
                audioElement.preservesPitch = false;
                audioElement.playbackRate = pitchSlider.value;
            }
        });

        // --- Preset Saver Logic ---
        let presets = JSON.parse(localStorage.getItem(STORAGE_KEY)) || {
            "Default Mix": { lpFreq: 1000, lpVol: 100, hpFreq: 1000, hpVol: 100, pitch: 1.0 },
            "Lo-Fi Sub Bass": { lpFreq: 200, lpVol: 140, hpFreq: 1500, hpVol: 20, pitch: 0.85 },
            "Crisp Highs Only": { lpFreq: 100, lpVol: 10, hpFreq: 3000, hpVol: 130, pitch: 1.15 }
        };

        function populatePresetDropdown() {
            presetSelect.innerHTML = '<option value="">-- Load a Preset --</option>';
            Object.keys(presets).forEach(presetName => {
                const option = document.createElement('option');
                option.value = presetName;
                option.textContent = presetName;
                presetSelect.appendChild(option);
            });
        }

        function applyPreset(name) {
            const preset = presets[name];
            if (!preset) return;

            // Update DOM Sliders
            lpSlider.value = preset.lpFreq;
            lpVolSlider.value = preset.lpVol;
            hpSlider.value = preset.hpFreq;
            hpVolSlider.value = preset.hpVol;
            pitchSlider.value = preset.pitch;

            // Update DOM Text Indicators
            lpVal.textContent = preset.lpFreq;
            lpVolVal.textContent = preset.lpVol;
            hpVal.textContent = preset.hpFreq;
            hpVolVal.textContent = preset.hpVol;
            pitchVal.textContent = parseFloat(preset.pitch).toFixed(2);

            // Update Live Audio Nodes if initialized
            if (isInitialized) {
                lowPassFilter.frequency.value = preset.lpFreq;
                lowPassGain.gain.value = preset.lpVol / 100;
                highPassFilter.frequency.value = preset.hpFreq;
                highPassGain.gain.value = preset.hpVol / 100;
            }

            // Update Pitch Element properties
            audioElement.preservesPitch = false;
            audioElement.playbackRate = preset.pitch;
        }

        savePresetBtn.addEventListener('click', () => {
            const name = presetNameInput.value.trim();
            if (!name) {
                alert('Please enter a valid name for your configuration!');
                return;
            }

            presets[name] = {
                lpFreq: parseInt(lpSlider.value),
                lpVol: parseInt(lpVolSlider.value),
                hpFreq: parseInt(hpSlider.value),
                hpVol: parseInt(hpVolSlider.value),
                pitch: parseFloat(pitchSlider.value)
            };

            localStorage.setItem(STORAGE_KEY, JSON.stringify(presets));
            populatePresetDropdown();
            presetSelect.value = name;
            presetNameInput.value = '';
        });

        deletePresetBtn.addEventListener('click', () => {
            const selected = presetSelect.value;
            if (!selected) {
                alert('Select a preset from the list first to delete it.');
                return;
            }

            if (confirm(`Delete configuration preset "${selected}"?`)) {
                delete presets[selected];
                localStorage.setItem(STORAGE_KEY, JSON.stringify(presets));
                populatePresetDropdown();
            }
        });

        presetSelect.addEventListener('change', (e) => {
            if (e.target.value) {
                applyPreset(e.target.value);
            }
        });

        // Initialize system preset dropdown on load
        populatePresetDropdown();
    </script>
</body>
</html>
