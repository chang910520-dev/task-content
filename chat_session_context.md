(function () {
  (function () {
    (function () {

      // ============================================================
      //  Deck 0 mini - SILENT SYNC KERNEL (V19.0 - SYSTEM EVOLUTION)
      //  Base: V18.0 (Underlying Version)
      //  Updates:
      //  1. CORE: Streaming State Machine (Robust Patching).
      //  2. UI: Integrated Code Editor & Diff View.
      //  3. UX: Timeline & Time Travel (Snapshot Manager).
      //  4. AI: Refactored Assistant & Expert Personas.
      // ============================================================

      const APP_ID = 'gemini-deck0-pro-omega';

      // ===================== DECK0 CONSTANTS =====================
      // External contracts remain legacy-prefixed; we only centralize access.
      const DECK0_STORAGE_KEYS = {
        SETTINGS_CLEAR_CHAT: 'deck0_setting_clear_chat',
        SETTINGS_AUDIO_URL: 'deck0_setting_audio_url',
        SETTINGS_FILE_SELECTIONS: 'deck0_setting_file_selections',
        STORAGE_MODE: 'deck0_storage_mode',
        MODEL: 'deck0_model',
        GH_REPO: 'deck0_gh_repo',
        GH_TOKEN: 'deck0_gh_token'
      };

      const DECK0_DOM_IDS = {
        MAIN: 'deck0-main-view',
        TASKS_BODY: 'deck0-tasks-body',
        FILES_BODY: 'deck0-files-body',
        STREAM_VIEW: 'deck0-stream-view',
        SEND_ICON_SVG: 'deck0-send-icon-svg',
        STATUS_BADGE: 'deck0-status-badge',
        STORAGE_INDICATOR: 'deck0-storage-ind'
      };

      const DECK0_SELECTORS = {
        PROMPT_TEXTAREA_PRIMARY: 'textarea[aria-label*="prompt"]',
        PROMPT_TEXTAREA_MS: 'ms-autosize-textarea textarea',
        PROMPT_TEXTAREA_FALLBACK: 'textarea',
        PROMPT_WRAPPER: 'ms-prompt-input-wrapper',
        PROMPT_FOOTER: 'footer',
        SYSTEM_PROMPT_DIALOG_TEXTAREA: 'mat-dialog-container textarea',
        SYSTEM_PROMPT_DIALOG_CLOSE_ICON: 'mat-dialog-container button[iconname="close"]',
        SYSTEM_PROMPT_DIALOG_CLOSE_TEST: 'mat-dialog-container button[data-test-close-button]',
        SYSTEM_INSTRUCTIONS_PANEL_BUTTON: 'ms-system-instructions-panel button',
        CHAT_TURN: 'ms-chat-turn',
        CHAT_TURN_MORE_OPTIONS_TOOLTIP_BUTTON: 'button[mattooltip="More options"]',
        MAT_MENU_ITEM: '.mat-mdc-menu-item',
        HOST_MS_TEXT_CHUNK: 'ms-text-chunk',
        HOST_TURN_EDIT_ICON: 'button .material-symbols-outlined',
        HOST_TURN_CANCEL_BUTTON: 'button[aria-label="Cancel"]',
        SEND_ICON_PAPER_PLANE: '.fa-paper-plane',
        SEND_BUTTON_PRIMARY: 'button[aria-label="Run"]',
        SEND_BUTTON_FALLBACK: 'button[aria-label="Send message"]',
        STOP_BUTTON_ARIA: 'button[aria-label*="Stop"]',
        UI_ACCORDION: '.deck0-accordion',
        UI_ACC_ARROW: '.deck0-acc-arrow',
        UI_BTN_MINI: '.deck0-btn-mini',
        UI_ACTIVE_FILE: '.active-file',
        UI_TREE_ITEM: '.deck0-tree-item',
        UI_CHECKBOX_CHECKED: 'input[type="checkbox"]:checked',
        UI_FILE_CHECKBOX_CHECKED: '.deck0-tree-item[data-kind="file"] input[type="checkbox"]:checked',
        UI_TREE_LABEL_SPAN_FLEX: 'span[style*="flex"]',
        UI_CHAT_INPUT: '.deck0-input-area',
        UI_CHAT_MESSAGE_CONTENT: '.deck0-msg-content',
        UI_DIFF_FILE_ITEM: '.deck0-diff-file-item',
        UI_PLAN_MARKER: '.deck0-plan-marker',
        ALL_BUTTONS: 'button'
      };

      const DECK0_NET_HINTS = {
        XHR_TARGET_URL_HINTS: ['GenerateContent', 'batchexecute']
      };

      // Internal debug flags (default OFF; enable manually in F12 if needed)
      const DECK0_DEBUG = {
        RENDER_CHANGED_FILES_VERBOSE: false
      };

      const TAG_COLORS = {
        'INFRA': {
          bg: 'rgba(142, 142, 147, 0.2)',
          text: '#98989d'
        },
        'CORE': {
          bg: 'rgba(10, 132, 255, 0.2)',
          text: '#64d2ff'
        },
        'API': {
          bg: 'rgba(191, 90, 242, 0.2)',
          text: '#da8fff'
        },
        'UI': {
          bg: 'rgba(255, 55, 95, 0.2)',
          text: '#ff6482'
        },
        'TEST': {
          bg: 'rgba(255, 159, 10, 0.2)',
          text: '#ffb340'
        }
      };

      // [TP-P4-2 Lite] THEME moved from UI block to Constants (L1 layer)
      const THEME = {
        bg: 'rgba(28, 28, 30, 0.95)',
        panelBg: 'rgba(0,0,0,0.3)',
        backdrop: 'blur(24px) saturate(180%)',
        border: '1px solid rgba(255, 255, 255, 0.08)',
        accent: '#0a84ff',
        success: '#30d158',
        danger: '#ff453a',
        warning: '#ff9f0a',
        purple: '#bf5af2',
        pink: '#ff375f',
        cyan: '#64d2ff',
        grey: '#8e8e93',
        textMain: '#ffffff',
        textDim: '#86868b',
        fontMono: '"SF Mono", "Menlo", "Monaco", "Courier New", monospace',
        font: '-apple-system, BlinkMacSystemFont, "SF Pro Text", "Segoe UI", sans-serif',
      };

      // ================= PROMPT TEMPLATE & INJECTION ENGINE =================
      // [DECK0 SECURITY] Obfuscated tokens to prevent self-parsing loops during meta-programming.
      // ORDERING LOGIC: Lifecycle (Phase 1 -> Phase 2) -> Hierarchy (Container -> Content -> Atomic)

      // ===================== DECK0 EFFECTS (S3) =====================
      const Deck0Effects = (() => {
        const persist = {
          get: (key) => {
            try { return localStorage.getItem(String(key)); } catch (e) { return null; }
          },
          set: (key, val) => {
            try { localStorage.setItem(String(key), String(val)); return true; } catch (e) { return false; }
          },
          jsonGet: (key, fallback) => {
            try {
              const raw = localStorage.getItem(String(key));
              if (!raw) return fallback;
              const parsed = JSON.parse(raw);
              return parsed === undefined ? fallback : parsed;
            } catch (e) {
              return fallback;
            }
          },
          jsonSet: (key, val) => {
            try { localStorage.setItem(String(key), JSON.stringify(val)); return true; } catch (e) { return false; }
          }
        };

        const dom = (() => {
          const qs = (sel, root = document) => {
            try { return (root || document).querySelector(sel); } catch (e) { return null; }
          };
          const qsa = (sel, root = document) => {
            try { return Array.from((root || document).querySelectorAll(sel)); } catch (e) { return []; }
          };

          const findPromptTextarea = () =>
            qs(DECK0_SELECTORS.PROMPT_TEXTAREA_PRIMARY) ||
            qs(DECK0_SELECTORS.PROMPT_TEXTAREA_MS) ||
            qs(DECK0_SELECTORS.PROMPT_TEXTAREA_FALLBACK);

          const findSendButton = () =>
            qs(DECK0_SELECTORS.SEND_BUTTON_PRIMARY) ||
            qs(DECK0_SELECTORS.SEND_BUTTON_FALLBACK);

          const findStopButton = () => {
            const direct = qs(DECK0_SELECTORS.STOP_BUTTON_ARIA);
            if (direct) return direct;

            const stopLabelTokens = ['stop', 'cancel', '停止', '取消'];
            const stopTextExact = ['Stop', 'Cancel', '停止', '取消'];
            const match = (btn) => {
              if (!btn || typeof btn.getAttribute !== 'function') return false;
              const aria = String(btn.getAttribute('aria-label') || '').trim();
              if (aria) {
                const low = aria.toLowerCase();
                if (stopLabelTokens.some(t => low.includes(t))) return true;
              }
              const spans = Array.from(btn.querySelectorAll('span'));
              return spans.some(s => stopTextExact.includes(String(s.textContent || '').trim()));
            };

            const roots = [
              qs(DECK0_SELECTORS.PROMPT_WRAPPER),
              qs(DECK0_SELECTORS.PROMPT_FOOTER),
              document
            ].filter(Boolean);

            for (const root of roots) {
              const hit = qsa(DECK0_SELECTORS.ALL_BUTTONS, root).find(match);
              if (hit) return hit;
            }
            return null;
          };

          return { qs, qsa, findPromptTextarea, findSendButton, findStopButton };
        })();

        // [S3-2] clock: 定时器封装（可追踪、可批量清理）
        const clock = (() => {
          const timers = new Map(); // id -> { type, nativeId }
          let nextId = 1;

          const _setTimeout = (fn, delay, ...args) => {
            const id = nextId++;
            const nativeId = setTimeout(() => {
              timers.delete(id);
              fn(...args);
            }, delay);
            timers.set(id, { type: 'timeout', nativeId });
            return id;
          };

          const _clearTimeout = (id) => {
            const entry = timers.get(id);
            if (entry && entry.type === 'timeout') {
              clearTimeout(entry.nativeId);
              timers.delete(id);
            }
          };

          const _setInterval = (fn, delay, ...args) => {
            const id = nextId++;
            const nativeId = setInterval(() => fn(...args), delay);
            timers.set(id, { type: 'interval', nativeId });
            return id;
          };

          const _clearInterval = (id) => {
            const entry = timers.get(id);
            if (entry && entry.type === 'interval') {
              clearInterval(entry.nativeId);
              timers.delete(id);
            }
          };

          // RAF 节流：确保回调最多每帧执行一次
          const rafThrottle = (fn) => {
            let rafId = null;
            let lastArgs = null;
            const throttled = (...args) => {
              lastArgs = args;
              if (rafId === null) {
                rafId = requestAnimationFrame(() => {
                  rafId = null;
                  fn(...lastArgs);
                });
              }
            };
            throttled.cancel = () => {
              if (rafId !== null) {
                cancelAnimationFrame(rafId);
                rafId = null;
              }
            };
            return throttled;
          };

          // 清理所有定时器（用于重置/卸载）
          const clearAll = () => {
            timers.forEach((entry, id) => {
              if (entry.type === 'timeout') clearTimeout(entry.nativeId);
              else if (entry.type === 'interval') clearInterval(entry.nativeId);
            });
            timers.clear();
          };

          // 调试：获取活跃定时器数量
          const activeCount = () => timers.size;

          return {
            setTimeout: _setTimeout,
            clearTimeout: _clearTimeout,
            setInterval: _setInterval,
            clearInterval: _clearInterval,
            rafThrottle,
            clearAll,
            activeCount
          };
        })();

        // [S3-3] net: 网络请求封装（统一错误处理）
        const net = (() => {
          // 通用 fetch 包装
          const request = async (url, options = {}) => {
            const res = await fetch(url, options);
            return { res, ok: res.ok, status: res.status };
          };

          // GET/POST JSON
          const fetchJson = async (url, options = {}) => {
            const { res, ok } = await request(url, options);
            const data = await res.json();
            if (!ok) {
              const err = new Error(data.message || data.error || res.statusText);
              err.status = res.status;
              err.data = data;
              throw err;
            }
            return data;
          };

          // GET/POST Text
          const fetchText = async (url, options = {}) => {
            const { res, ok } = await request(url, options);
            const text = await res.text();
            if (!ok) {
              const err = new Error(text || res.statusText);
              err.status = res.status;
              throw err;
            }
            return text;
          };

          // POST JSON (convenience)
          const postJson = async (url, body, extraHeaders = {}) => {
            return fetchJson(url, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json', ...extraHeaders },
              body: JSON.stringify(body)
            });
          };

          return { fetchJson, fetchText, postJson };
        })();

        return { persist, dom, clock, net };
      })();
      window.__DECK0_EFFECTS = Deck0Effects;

      // ===================== DECK0 CORE UTILS =====================
      // 模块作用域的纯工具函数（供 Deck0.core.util 导出）
      const Deck0CoreUtils = {
        // 正则转义
        escapeRegExp: (str) => String(str || '').replace(/[.*+?^${}()|[\]\\]/g, '\\$&'),
        // 获取文件扩展名（不含点）
        getExtension: (path) => {
          const s = String(path || '');
          const i = s.lastIndexOf('.');
          // 无点 或 点在末尾 → 无扩展名
          if (i < 0 || i === s.length - 1) return '';
          return s.slice(i + 1);
        },
        // 获取文件名（含扩展名）
        getFileName: (path) => {
          const s = String(path || '').replace(/\\/g, '/');
          return s.split('/').pop() || '';
        },
        // 获取父目录路径
        getParentPath: (path) => {
          const s = String(path || '').replace(/\\/g, '/');
          const parts = s.split('/').filter(p => p);
          parts.pop();
          return parts.join('/');
        },
        // 计算行数
        countLines: (content) => {
          const s = String(content || '');
          return s ? s.split('\n').length : 0;
        },
        // 截断内容（保留前 N 行）
        truncateLines: (content, maxLines = 100, suffix = '\n... (truncated)') => {
          const s = String(content || '');
          const lines = s.split('\n');
          if (lines.length <= maxLines) return s;
          return lines.slice(0, maxLines).join('\n') + suffix;
        }
      };

      const _T = {
        // === PHASE 1: ARCHITECTURE (Macro) ===
        ga: 'graph' + '_analysis', // #1  (Analysis Block)
        m_s: 'MANIFEST' + '_START', // #2  (Manifest Container Start)
        m_e: 'MANIFEST' + '_END', // #3  (Manifest Container End)

        // === PHASE 2: ORCHESTRATION (Container) ===
        p_s: 'PLAN' + '_START', // #4  (Atomic Plan Start)
        p_e: 'PLAN' + '_END', // #5  (Atomic Plan End)

        // === PHASE 2: LOGIC & ACTION (Content) ===
        td: 'technical' + '_design', // #6  (Design Block)
        op: 'OP', // #7  (Operation Header)

        // === PHASE 2: SYNTAX & PATCHING (Fence Protocol V2) ===
        // [CRITICAL] Using concatenation to hide these strings from the parser itself
        xml: '<' + '<<<<< ' + 'SEARCH', // #8  (Start Fence)
        rp: '=' + '=====', // #10 (Middle Separator)
        ce: '>' + '>>>>> ' + 'REPLACE' // #12 (End Fence)
      };

      // [PART A: EXPERT PERSONAS]

      const TitanHighlighter = {
        theme: {
          text: '#d4d4d4', // Main text
          comment: '#6a9955', // Green
          string: '#ce9178', // Orange
          keyword: '#569cd6', // Blue (var, const, function)
          control: '#c586c0', // Pink (if, return)
          type: '#4ec9b0', // Teal (Classes, Interfaces)
          func: '#dcdcaa', // Yellow (Calls, Definitions)
          number: '#b5cea8', // Light Green
          boolean: '#569cd6', // Blue
          property: '#9cdcfe', // Light Blue (Object props)
          variable: '#9cdcfe', // Light Blue
          operator: '#d4d4d4', // White/Grey
          punctuation: '#ffd700', // Gold brackets
          tag: '#569cd6', // JSX Tag
          attr: '#9cdcfe', // JSX Attribute
          decorator: '#dcdcaa', // Decorator Yellow
          regex: '#d16969' // Reddish
        },

        // Heuristics for Regex Literals:
        // A slash is a regex start if the previous token was a Keyword, Operator, or Punctuation (not closing paren usually).
        // Simplified: We assume syntax is correct.

        tokenize: (text, ext = 'js') => {
          if (!text) return [{
            part: ' ',
            color: null
          }];

          // 1. Definition of Grammars (Priority Order)
          // "Opaque" tokens consume text so it can't be re-matched.
          const grammar = [
            // Comments (Block & Line)
            {
              type: 'comment',
              pattern: /\/\/.*|\/\*[\s\S]*?\*\//y
            },

            // Strings (Template, Double, Single) - Handle escapes
            {
              type: 'string',
              pattern: /(`(?:\\.|[^`])*`|"(?:\\.|[^"\n])*"|'(?:\\.|[^'\n])*')/y
            },

            // Regex Literal (Naive but effective for most cases)
            // Looks for /.../ followed by flags. Avoids matching comments or division (heuristics inside loop).
            // We'll treat it as low priority or check context. For this engine, we use a safe approximation:
            // Match / pattern / only if it doesn't look like division.
            // (Skip strict regex support to avoid false positives on division, trade-off for stability)

            // JSX/HTML Tags (Basic)
            {
              type: 'tag',
              pattern: /<\/?[a-zA-Z0-9.-]+(?:\s+[a-zA-Z0-9.-]+(?:=(?:"[^"]*"|'[^']*'|{[^}]*}))?)*\s*\/?>/y
            },

            // Decorators
            {
              type: 'decorator',
              pattern: /@[a-zA-Z_$][a-zA-Z0-9_$]*/y
            },

            // Numbers (Hex, Float, Int)
            {
              type: 'number',
              pattern: /\b(?:0x[0-9a-fA-F]+|\d+(?:_\d+)*(?:\.\d*)?(?:e[+-]?\d+)?)\b/y
            },

            // Control Keywords (Flow)
            {
              type: 'control',
              pattern: /\b(?:if|else|return|throw|try|catch|finally|switch|case|default|break|continue|for|while|do|await|yield)\b/y
            },

            // Storage Keywords (Decl)
            {
              type: 'keyword',
              pattern: /\b(?:const|let|var|function|class|extends|implements|import|export|from|async|new|this|super|typeof|void|delete|in|instanceof|debugger|interface|type|enum|package|private|protected|public|static|readonly)\b/y
            },

            // Booleans / Null
            {
              type: 'boolean',
              pattern: /\b(?:true|false|null|undefined)\b/y
            },

            // Types (PascalCase words)
            {
              type: 'type',
              pattern: /\b[A-Z][a-zA-Z0-9_$]*\b/y
            },

            // Functions (Identifier followed by () - Lookahead handled manually or via group)
            // We split this: if we match an identifier, we peek ahead.
            {
              type: 'identifier',
              pattern: /[a-zA-Z_$][a-zA-Z0-9_$]*/y
            },

            // Punctuation
            {
              type: 'punctuation',
              pattern: /[{}()\[\];,]/y
            },

            // Operators
            {
              type: 'operator',
              pattern: /[+\-*/%=&|<>!^?:.]/y
            },

            // Whitespace
            {
              type: 'space',
              pattern: /\s+/y
            }
          ];

          const tokens = [];
          let cursor = 0;
          const len = text.length;

          while (cursor < len) {
            let matched = false;

            for (const rule of grammar) {
              rule.pattern.lastIndex = cursor; // Sticky regex
              const m = rule.pattern.exec(text);

              if (m) {
                const content = m[0];
                matched = true;

                // Refine Identifier (Function vs Variable vs Property)
                if (rule.type === 'identifier') {
                  // Peek next char for '('
                  let nextCharIdx = cursor + content.length;
                  while (nextCharIdx < len && /\s/.test(text[nextCharIdx])) nextCharIdx++;
                  const isFunc = text[nextCharIdx] === '(';

                  // Peek prev for '.' (Property access)
                  let prevCharIdx = cursor - 1;
                  while (prevCharIdx >= 0 && /\s/.test(text[prevCharIdx])) prevCharIdx--;
                  const isProp = text[prevCharIdx] === '.';

                  let color = TitanHighlighter.theme.variable;
                  if (isFunc) color = TitanHighlighter.theme.func;
                  else if (isProp) color = TitanHighlighter.theme.property;
                  else if (content === 'this' || content === 'super') color = TitanHighlighter.theme.keyword;

                  tokens.push({
                    part: content,
                    color: color
                  });
                } else if (rule.type === 'tag') {
                  // Simple heuristic for tags: Highlight <Tag and attributes separately? 
                  // For stability, just color the whole tag logic roughly or split it.
                  // Let's just color the whole tag block as 'tag' for now, or refine.
                  // V2 Refinement: Split the tag content.
                  // For now, simpler:
                  tokens.push({
                    part: content,
                    color: TitanHighlighter.theme.keyword
                  });
                } else {
                  tokens.push({
                    part: content,
                    color: TitanHighlighter.theme[rule.type] || TitanHighlighter.theme.text
                  });
                }

                cursor += content.length;
                break;
              }
            }

            if (!matched) {
              // Fallback: Consume one char
              tokens.push({
                part: text[cursor],
                color: TitanHighlighter.theme.text
              });
              cursor++;
            }
          }

          return tokens;
        }
      };

      const DiffEngine = {
        compute: (oldText, newText) => {
          const a = oldText ? oldText.split(/\r?\n/) : [];
          const b = newText ? newText.split(/\r?\n/) : [];
          const n = a.length;
          const m = b.length;
          const max = n + m;

          // Myers Diff Algorithm (Greedy LCS)
          const v = {
            1: 0
          };
          const trace = [];

          for (let d = 0; d <= max; d++) {
            trace.push({
              ...v
            });
            for (let k = -d; k <= d; k += 2) {
              let x;
              if (k === -d || (k !== d && v[k - 1] < v[k + 1])) {
                x = v[k + 1];
              } else {
                x = v[k - 1] + 1;
              }

              let y = x - k;
              while (x < n && y < m && a[x] === b[y]) {
                x++;
                y++;
              }

              v[k] = x;

              if (x >= n && y >= m) {
                return DiffEngine._traceback(trace, a, b);
              }
            }
          }
          return [];
        },

        _traceback: (trace, a, b) => {
          const diff = [];
          let x = a.length;
          let y = b.length;

          for (let d = trace.length - 1; d >= 0; d--) {
            const v = trace[d];
            const k = x - y;
            let prevK;

            if (k === -d || (k !== d && v[k - 1] < v[k + 1])) {
              prevK = k + 1;
            } else {
              prevK = k - 1;
            }

            const prevX = v[prevK];
            const prevY = prevX - prevK;

            while (x > prevX && y > prevY) {
              diff.unshift({
                type: 'eq',
                content: a[x - 1]
              });
              x--;
              y--;
            }

            if (d > 0) {
              if (x === prevX) {
                diff.unshift({
                  type: 'add',
                  content: b[y - 1]
                });
                y--;
              } else if (y === prevY) {
                diff.unshift({
                  type: 'del',
                  content: a[x - 1]
                });
                x--;
              }
            }
          }
          return diff;
        }
      };
      // [V19.0 LOGIC] Expose for Architecture

      // ===================== DECK0 STATE (S2) =====================
      // Store + Actions: 状态树与变更入口
      // - createStore: 状态工厂（getState/replace/mutate/subscribe）
      // - Deck0Actions: 状态变更唯一通道（runtime/project/settings/session/protocol）

      // [V19.0 INFRA] Feature Flags (T7-2a: hardcoded ON)
      // All Phase flags are now permanently enabled; legacy branches remain only as a safety net.

      // [V19.0 CORE] Store Factory (Pattern 2.2)
      const createStore = (initial) => {
        let state = initial;
        const listeners = new Set();
        const notify = () => listeners.forEach(cb => cb(state));
        return {
          getState: () => state,
          // Replacement: Change top-level key reference (for resets)
          replace: (key, val) => {
            state = {
              ...state,
              [key]: val
            };
            notify();
          },
          // Mutation: Mutate internal properties in-place (Legacy compatibility)
          mutate: (key, fn) => {
            if (state[key]) fn(state[key]);
            notify();
          },
          // Deduplication Helper for SyncList
          addSyncPath: (path) => {
            // Guard against partial init
            if (!state.session) return;
            const list = state.session.syncList;
            if (Array.isArray(list) && !list.includes(path)) list.push(path);
            notify();
          },
          subscribe: (cb) => {
            listeners.add(cb);
            return () => listeners.delete(cb);
          }
        };
      };

      // [V19.0 CORE] Persisted Settings (T7-2b)
      const PERSISTED = (() => {
        const fileSelections = Deck0Effects.persist.jsonGet(DECK0_STORAGE_KEYS.SETTINGS_FILE_SELECTIONS, {});
        return {
          clearChat: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.SETTINGS_CLEAR_CHAT) !== 'false',
          audioUrl: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.SETTINGS_AUDIO_URL) || '',
          fileSelections: (fileSelections && typeof fileSelections === 'object') ? fileSelections : {},
          storageMode: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.STORAGE_MODE) || 'LOCAL',
          model: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.MODEL) || 'gemini-3-pro-preview'
        };
      })();

      // [V19.0 CORE] State Tree Schema (T1-2)
      const initialState = {
        // Runtime Core (Legacy: DECK0_RUN_STATE, activeStreamOwner)
        runtime: {
          runId: 0,
          isPipelineRunning: false,
          finalizeDoneRunId: null,
          generationDone: false,
          autoExecutionDone: false,
          autoDiffOpenedRunId: null,
          lastAutoDiffRunId: null,
          activeProviderId: 'gemini',
          activeStreamOwner: null
        },
        // Project Config (Legacy: projectDirHandle, DECK0_SETTINGS.fileSelections)
        project: {
          storageMode: PERSISTED.storageMode, // 'LOCAL'|'VIRTUAL'|'LINK'
          dirHandle: null,      // Native FileSystemDirectoryHandle (Ref)
          baselineSnapshot: null, // Array<{path, content}> | null
          fileSelections: PERSISTED.fileSelections,   // { ProjectName: [excluded_paths] }
          ignoreList: "node_modules, .git, dist, build, package-lock.json, .DS_Store, .vscode"
        },
        // Session Side-Effects (Legacy: sessionSyncList, sessionCreatedFiles)
        session: {
          syncList: [],         // [path] - Set semantics
          createdFiles: [],     // [path]
          executionLog: [],     // [{id, title, desc, files}]
          branchSnapshot: null, // Array<{path, content, status}> | null
          lastUserInstruction: ""
        },
        // Protocol Parsing (Legacy: store.getState().protocol.plans, lastAnswerMarkdown)
        protocol: {
          manifest: null,
          plans: [],            // [{id, title, desc, status, ops}]
          manifestParsed: false,
          lastAnswerMarkdown: ""
        },
        // Timeline (Legacy: timelineSnapshots)
        timeline: {
          snapshots: []
        },
        // View State (New Isolation Strategy)
        view: {
          kind: 'normal',       // 'normal'|'diff'|'time_travel'
          preview: false,
          readOnly: false,
          focusPath: null,
          snapshotId: null,
          baseMap: new Map(),
          nextMap: new Map(),
          statusMap: new Map()
        },
        // Legacy Bridge Container (Strict Isolation)
        legacy: {
          DECK0_VIEW: null        // Legacy Deck0.store.getState().legacy.DECK0_VIEW object
        },
        // User Settings (Legacy: DECK0_SETTINGS, etc)
        settings: {
          clearChat: PERSISTED.clearChat,
          audioUrl: PERSISTED.audioUrl,
          interactionMode: 'PROTOCOL',
          model: PERSISTED.model,
          expertId: 'ARCHITECT',
          includeCodeContext: true
        },
        // UI Ephemerals
        ui: {
          activeTermTab: 'logs',
          tasksAccordionOpen: false,
          filesAccordionOpen: false,
          editorActivePath: null,
          fileSelectionMap: new Map(),
          renderSeq: 0
        }
      };

      // [V19.0 CORE] Store Instantiation (T1-3)
      const store = createStore(initialState);
      // Expose for debugging
      window.__DECK0_STORE = store;

      // ===================== DECK0 ACTIONS (S2) =====================
      // Centralize all state writes through a single API to avoid scattered mutations.
      const Deck0Actions = (() => {
        const asString = (v) => String(v == null ? '' : v);
        const clearArray = (arr) => { if (Array.isArray(arr)) arr.length = 0; };
        const replaceArrayContents = (arr, next) => {
          if (!Array.isArray(arr)) return;
          arr.length = 0;
          if (Array.isArray(next)) arr.push(...next);
        };

        return {
          runtime: {
            beginRun: () => store.mutate('runtime', (r) => {
              if (!r) return;
              r.runId = (Number(r.runId) || 0) + 1;
              r.generationDone = false;
              r.autoExecutionDone = false;
            }),
            resetAutoDiffLocks: () => store.mutate('runtime', (r) => {
              if (!r) return;
              r.autoDiffOpenedRunId = null;
              r.lastAutoDiffRunId = null;
            }),
            setFinalizeDoneRunId: (runId) => store.mutate('runtime', (r) => {
              if (!r) return;
              const rid = runId == null ? null : (Number(runId) || 0);
              r.finalizeDoneRunId = rid;
            }),
            markGenerationDone: () => store.mutate('runtime', (r) => {
              if (!r) return;
              r.generationDone = true;
            }),
            markAutoExecutionDone: () => store.mutate('runtime', (r) => {
              if (!r) return;
              r.autoExecutionDone = true;
            }),
            setLastAutoDiffRunId: (runId) => store.mutate('runtime', (r) => {
              if (!r) return;
              r.lastAutoDiffRunId = runId == null ? null : (Number(runId) || 0);
            }),
            setPipelineRunning: (running) => store.mutate('runtime', (r) => {
              if (!r) return;
              r.isPipelineRunning = !!running;
            }),
            setAutoDiffOpenedRunId: (runId) => store.mutate('runtime', (r) => {
              if (!r) return;
              r.autoDiffOpenedRunId = runId;
            })
          },
          project: {
            setStorageMode: (mode) => {
              const next = String(mode || 'LOCAL');
              store.mutate('project', (p) => { if (p) p.storageMode = next; });
              Deck0Effects.persist.set(DECK0_STORAGE_KEYS.STORAGE_MODE, next);
            },
            // [TP-P3-3] EditorManager baseline management
            ensureBaselineEntry: (path, content) => store.mutate('project', (p) => {
              if (!p) return;
              if (!Array.isArray(p.baselineSnapshot)) p.baselineSnapshot = [];
              const normPath = String(path || '').trim();
              if (!normPath) return;
              const hasBase = p.baselineSnapshot.some(f => String(f.path || '').trim() === normPath);
              if (!hasBase) {
                p.baselineSnapshot.push({
                  path: normPath,
                  content: String(content || '')
                });
              }
            }),
            updateFileSelections: (key, list) => store.mutate('project', p => {
              if (!p) return;
              if (!p.fileSelections) p.fileSelections = {};
              const keys = Object.keys(p.fileSelections);
              if (!keys.includes(key) && keys.length >= 3) {
                delete p.fileSelections[keys[0]];
              }
              p.fileSelections[key] = list;
            })
          },
          settings: {
            setClearChat: (enabled) => {
              const next = !!enabled;
              store.mutate('settings', (s) => { if (s) s.clearChat = next; });
              Deck0Effects.persist.set(DECK0_STORAGE_KEYS.SETTINGS_CLEAR_CHAT, next);
            },
            setAudioUrl: (url) => {
              const next = String(url || '');
              store.mutate('settings', (s) => { if (s) s.audioUrl = next; });
              Deck0Effects.persist.set(DECK0_STORAGE_KEYS.SETTINGS_AUDIO_URL, next);
            },
            setModel: (model) => {
              const next = String(model || '');
              store.mutate('settings', (s) => { if (s) s.model = next; });
              Deck0Effects.persist.set(DECK0_STORAGE_KEYS.MODEL, next);
            }
          },
          session: {
            setLastUserInstruction: (text) => store.mutate('session', (s) => {
              if (!s) return;
              s.lastUserInstruction = asString(text).trim();
            }),
            resetPendingChanges: () => store.mutate('session', (s) => {
              if (!s) return;
              clearArray(s.syncList);
              clearArray(s.createdFiles);
            }),
            addSyncPath: (path) => store.addSyncPath(path),
            removeSyncPath: (path) => store.mutate('session', s => {
              if (s && s.syncList) s.syncList = s.syncList.filter(x => normalizeVfsPath(x) !== normalizeVfsPath(path));
            }),
            addCreatedFile: (path) => store.mutate('session', s => {
              if (s && s.createdFiles && !s.createdFiles.includes(path)) s.createdFiles.push(path);
            }),
            removeCreatedFile: (path) => store.mutate('session', s => {
              if (s && s.createdFiles) s.createdFiles = s.createdFiles.filter(x => normalizeVfsPath(x) !== normalizeVfsPath(path));
            }),
            clearExecutionLog: () => store.mutate('session', s => {
              if (s) s.executionLog = [];
            }),
            removeExecutionLogItem: (id) => store.mutate('session', s => {
              if (s && s.executionLog) s.executionLog = s.executionLog.filter(item => item.id !== id);
            }),
            addExecutionLogItem: (item) => store.mutate('session', s => {
              if (s && s.executionLog && !s.executionLog.some(c => c.id === item.id)) {
                s.executionLog.push(item);
              }
            })
          },
          protocol: {
            resetForNewRun: () => store.mutate('protocol', (p) => {
              if (!p) return;
              clearArray(p.plans);
              p.manifestParsed = false;
              p.lastAnswerMarkdown = '';
            }),
            appendAnswerChunk: (chunk) => {
              const text = asString(chunk);
              if (!text) return store.getState().protocol.lastAnswerMarkdown;
              store.mutate('protocol', (p) => {
                if (!p) return;
                p.lastAnswerMarkdown = asString(p.lastAnswerMarkdown) + text;
              });
              return store.getState().protocol.lastAnswerMarkdown;
            },
            setPlansIfEmpty: (plans) => store.mutate('protocol', (p) => {
              if (!p) return;
              if (Array.isArray(p.plans) && p.plans.length === 0) replaceArrayContents(p.plans, plans);
              else if (!Array.isArray(p.plans) || p.plans.length === 0) p.plans = plans;
            }),
            updatePlans: (plans) => store.mutate('protocol', (p) => {
              if (p) p.plans = plans;
            }),
            setManifestParsed: (val) => store.mutate('protocol', p => { if (p) p.manifestParsed = !!val; })
          },
          legacy: {
            setView: (view) => store.mutate('legacy', l => { if (l) l.DECK0_VIEW = view; }),
            clearView: () => store.mutate('legacy', l => { if (l) l.DECK0_VIEW = null; }),
            setPreview: (enabled) => store.mutate('legacy', l => { if (l && l.DECK0_VIEW) l.DECK0_VIEW.preview = !!enabled; }),
            togglePreview: () => store.mutate('legacy', l => { if (l && l.DECK0_VIEW) l.DECK0_VIEW.preview = !l.DECK0_VIEW.preview; }),
            setVirtualContent: (path, content) => store.mutate('legacy', l => {
              if (l && l.DECK0_VIEW && l.DECK0_VIEW.virtualMap) l.DECK0_VIEW.virtualMap.set(path, String(content || ''));
            })
          },
          ui: {
            incrementRenderSeq: () => store.mutate('ui', u => {
              if (u) u.renderSeq = (u.renderSeq || 0) + 1;
            })
          }
        };
      })();
      window.__DECK0_ACTIONS = Deck0Actions;


      // 0. Cleanup
      if (window.deck0KernelActive) {
        console.log("♻️ Upgrading to V18.0 (System Evolution)...");
        if (window.originalXHR) window.XMLHttpRequest = window.originalXHR;
        if (window.originalFetch) window.fetch = window.originalFetch;
        const old = Deck0.ui.refs.getById(APP_ID);
        if (old) old.remove();
      }
      window.deck0KernelActive = true;

      // ===================== DECK0 UI =====================
      // UI 层：主题/样式/DOM工厂/渲染函数/编辑器/设置面板
      // - THEME/CSS: 样式常量与注入
      // - DOM Factory: SVG-aware 元素创建
      // - EditorManager: 代码编辑器
      // - renderXxx/updateXxx: UI 渲染函数
      // - BridgeUIAdapter: 流式输出适配器

      // [S6-2] UI Refs: 集中管理 DOM 节点获取（懒加载缓存）
      const Deck0UIRefs = (() => {
        const cache = new Map();

        // 静态元素 getter（按 DECK0_DOM_IDS 定义）
        const get = (key) => {
          const id = DECK0_DOM_IDS[key];
          if (!id) return null;
          let el = cache.get(key);
          if (el && el.isConnected) return el;
          el = Deck0.ui.refs.getById(id);
          if (el) cache.set(key, el);
          return el;
        };

        // 动态 ID getter（如 plan-${id}）
        const getById = (id) => document.getElementById(id);

        // 清除缓存（用于 DOM 重建后）
        const invalidate = (key) => {
          if (key) cache.delete(key);
          else cache.clear();
        };

        return {
          // 静态元素快捷访问
          get MAIN() { return get('MAIN'); },
          get tasksBody() { return get('TASKS_BODY'); },
          get filesBody() { return get('FILES_BODY'); },
          get streamView() { return get('STREAM_VIEW'); },
          get sendIconSvg() { return get('SEND_ICON_SVG'); },
          get statusBadge() { return get('STATUS_BADGE'); },
          get storageIndicator() { return get('STORAGE_INDICATOR'); },
          // 方法
          get,
          getById,
          invalidate
        };
      })();

      // ================= CONFIG & EXPERTS =================
      // [TP-P4-2 Lite] THEME moved to DECK0 CONSTANTS section (L1 layer)

      const EXPERTS = {
        'ARCHITECT': {
          id: 'ARCHITECT',
          name: 'System Architect',
          color: '#0a84ff',
          desc: '// Describe task. System Architect will analyze dependency graph...',
          persona: `You are Deck0-Orchestrator, a World-Class System Architect & Engineering Lead.
	Your goal is to solve complex coding tasks by constructing a STRICT DEPENDENCY GRAPH and executing it with atomic precision.`
        },
        'DEBUGGER': {
          id: 'DEBUGGER',
          name: 'Bug Hunter',
          color: '#ff453a',
          desc: '// Paste error logs. Bug Hunter will diagnose and fix...',
          persona: `You are Deck0-Debugger, an Elite Bug Hunter & Stability Expert.
	Your goal is to analyze error logs, fix infinite loops/memory leaks, and repair broken logic WITHOUT breaking existing features.
	You prioritize SAFETY and MINIMAL changes.`
        },
        'UI_UX': {
          id: 'UI_UX',
          name: 'UI/UX Designer',
          color: '#ff375f',
          desc: '// Describe look & feel. UI Designer will polish styles...',
          persona: `You are Deck0-Designer, a World-Class UI/UX Specialist (Tailwind/CSS3/Animations).
	Your goal is to create visually stunning, accessible, and responsive interfaces. 
	You care deeply about spacing, typography, motion, and color harmony.`
        },
        'SECURITY': {
          id: 'SECURITY',
          name: 'Security Eng',
          color: '#30d158',
          desc: '// Describe logic. Security Eng will harden auth & data...',
          persona: `You are Deck0-Sec, a Senior Cybersecurity Engineer.
	Your goal is to identify vulnerabilities (XSS, CSRF, Injection) and implement robust authentication/authorization logic.
	You favor strict validation and secure defaults.`
        },
        'QA_TEST': {
          id: 'QA_TEST',
          name: 'QA Engineer',
          color: '#ff9f0a',
          desc: '// Describe scope. QA will write Tests (Unit/E2E)...',
          persona: `You are Deck0-QA, a Lead Test Automation Engineer.
	Your goal is to write comprehensive Unit Tests (Jest/Vitest) and E2E Tests.
	You ensure 100% coverage of critical paths and edge cases.`
        },
        'REFACTOR': {
          id: 'REFACTOR',
          name: 'Code Refactorer',
          color: '#bf5af2',
          desc: '// Point to messy code. Refactorer will clean it up...',
          persona: `You are Deck0-CleanCode, a Refactoring Specialist.
	Your goal is to reduce technical debt, improve readability, and optimize performance.
	You do not change behavior, only structure and efficiency.`
        }
      };

      // [PART B: OUTPUT PROTOCOL]
      // #1=graph_analysis, #2=MANIFEST_START, #3=MANIFEST_END
      // #4=PLAN_START, #5=PLAN_END, #6=technical_design, #7=OP
      // #8=Start Fence, #10=Separator, #12=End Fence

      const DECK0_PROTOCOL_TEMPLATE = `
【GLOBAL RULES】
1.  **NO FLUFF**: Outside of specific analysis blocks, output is strictly structural.
2.  **PARSABILITY**: Use the provided strict delimiters for all machine-readable content.
3.  **ID FORMAT**: Plan IDs MUST be alphanumeric with hyphens only (e.g., \`CORE-01\`, \`UI-02\`).
4.  **USE PROVIDED CONTEXT**: Only generate PATCH SEARCH blocks by copying from the supplied \`[PROJECT CONTEXT]\` file contents (never guess).

---

【PHASE 1: ARCHITECTURAL GRAPH & MANIFEST】
Before generating the JSON, you MUST perform a "Dependency Analysis" to determine the execution order (Critical Path).

**ID NAMING CONVENTION (STRICT):**
Use semantic prefixes based on the architectural layer:
- \`INFRA-xx\`: Config, Env, Build scripts, Package.json
- \`CORE-xx\`: Base classes, Types, Utils, Database Schema
- \`API-xx\`: Backend logic, Services, API routes
- \`UI-xx\`: Frontend components, CSS, Views
- \`TEST-xx\`: Unit tests, E2E tests

**TITLE NAMING CONVENTION (Conventional Commits):**
Start titles with: \`feat:\`, \`fix:\`, \`refactor:\`, \`chore:\`, \`style:\`.

**OUTPUT FORMAT FOR PHASE 1**:

  <#1>
  (Thinking process: Identify layers. DB must come before API. API before UI.
  Checks for circular dependencies.)
  </#1>

	  [[DECK0_#2]]
  {
    "root_goal": "Implement User Authentication",
    "plans": [
      {
        "id": "INFRA-01",
        "title": "chore: Setup Supabase Client",
        "desc": "Install @supabase/js and configure .env variables.",
        "deps": []
      },
      {
        "id": "CORE-01",
        "title": "refactor: Define Auth Types",
        "desc": "Create IAuthUser interface in types/auth.ts.",
        "deps": ["INFRA-01"]
      }
    ]
  }
	  [[DECK0_#3]]

---

【PHASE 2: ATOMIC EXECUTION (HIGH QUALITY)】
Execute each plan from the JSON sequentially.
To ensure QUALITY, you must "Design before you Code" for every step.

**CRITICAL RULES FOR PATCHING (Git-Fence Protocol):**
1.  **NO LAZINESS / NO TRUNCATION**: Inside the SEARCH block, every character MUST match the original file exactly. Using \`// ...\` or skipping lines is **STRICTLY FORBIDDEN** and will cause the patch to fail.
2.  **MINIMAL ANCHORING**: Do NOT include entire functions. Use **3-5 lines of UNIQUE context** above and below the change to anchor the search.
3.  **OBFUSCATION IS MANDATORY**: When generating code that *contains* the fence markers (like \`<<<<<< SEARCH\`), you **MUST** construct them dynamically using string concatenation (e.g., \`'<' + '<<<<< ' + 'SEARCH'\`).
4.  **FENCE SYNTAX**: Each fence marker (\`#8\`, \`#10\`, \`#12\`) MUST be on its own line with NO extra spaces.

**OUTPUT FORMAT FOR PHASE 2**:

	  [[DECK0_#4:ID]]

  <#6>
  ### Approach
  (How you will implement this. Mention Design Patterns.)
  ### Risks & Edge Cases
  (What could break? e.g., "Null checks needed for token")
  </#6>

		  ### DECK0_#7: [WRITE/PATCH/DELETE] path/to/file.ext
	  (Brackets are literal + mandatory on the op type.)
	  > 📝 **Commit Msg**: (Concise summary of changes)

  (IF NEW FILE/REWRITE -> Markdown)
  \`\`\`ext
  (Code)
  \`\`\`

  (IF EDIT -> Git-Fence Protocol)
  #8
  (EXACT ORIGINAL CODE - 3-5 lines context)
  #10
  (NEW CODE)
  #12

	  [[DECK0_#5:ID]]`;

      // Dynamic Splicer to Generate the Real Prompt at Runtime
      const getSystemPrompt = () => {
        const expert = EXPERTS[Deck0.store.getState().settings.expertId] || EXPERTS['ARCHITECT'];
        const fullTemplate = `${expert.persona}\n\n${DECK0_PROTOCOL_TEMPLATE}`;

        // [DECK0 CRITICAL] Replacements must be ordered DESCENDING
        return fullTemplate
          // Phase 2: Micro/Atomic (Double Digits First)
          .replace(/#12/g, _T.ce)
          .replace(/#10/g, _T.rp)
          .replace(/#8/g, _T.xml)
          // Phase 2: Content
          .replace(/#7/g, _T.op)
          .replace(/#6/g, _T.td)
          // Phase 2: Container
          .replace(/#5/g, _T.p_e)
          .replace(/#4/g, _T.p_s)
          // Phase 1: Macro
          .replace(/#3/g, _T.m_e)
          .replace(/#2/g, _T.m_s)
          .replace(/#1/g, _T.ga);
      };

      // Global State

      // [V16.6] Settings State
      /*
      const DECK0_SETTINGS = {
        clearChat: localStorage.getItem('deck0_setting_clear_chat') !== 'false', // Default true
        audioUrl: localStorage.getItem('deck0_setting_audio_url') || '',
        interactionMode: 'PROTOCOL', // Fixed to PROTOCOL mode
        // Format: { "ProjectName": ["path/to/exclude", ...] }
        fileSelections: JSON.parse(localStorage.getItem('deck0_setting_file_selections') || '{}')
      };
      */

      const saveSettings = () => {
        Deck0Effects.persist.set(DECK0_STORAGE_KEYS.SETTINGS_CLEAR_CHAT, Deck0.store.getState().settings.clearChat);
        Deck0Effects.persist.set(DECK0_STORAGE_KEYS.SETTINGS_AUDIO_URL, Deck0.store.getState().settings.audioUrl);
        Deck0Effects.persist.jsonSet(DECK0_STORAGE_KEYS.SETTINGS_FILE_SELECTIONS, Deck0.store.getState().project.fileSelections);
      };

      let completionAudio = null;

      const LINK_API = 'http://localhost:6060';
      const LinkAdapter = {
        check: async () => {
          try {
            return await Deck0Effects.net.fetchJson(`${LINK_API}/status`);
          } catch (e) {
            throw new Error('Deck0 Link not reachable. Start the Link server.');
          }
        },
        getTree: async () => {
          // _t timestamp handling is internal to the URL construction if needed, 
          // but Deck0Effects doesn't auto-add it. We keep it.
          return await Deck0Effects.net.fetchJson(`${LINK_API}/tree?_t=${Date.now()}`);
        },
        read: async (path) => {
          const data = await Deck0Effects.net.postJson(`${LINK_API}/read`, { path });
          return data.content;
        },
        write: async (path, content) => {
          await Deck0Effects.net.postJson(`${LINK_API}/write`, { path, content });
        },
        delete: async (path) => {
          await Deck0Effects.net.postJson(`${LINK_API}/delete`, { path });
        },
        reveal: async (path) => {
          await Deck0Effects.net.postJson(`${LINK_API}/reveal`, { path });
        }
      };
      let lastTreeKeysStr = ''; // [V18.0 Fix] Prevent flicker
      let lastViewModeKey = ''; // [V18.14 Fix] Track View Mode changes
      // [TP-P5-1] Migrated Globals
      let _contextBuffer = null;
      let _firstDiffablePath = null;
      let _linkIntervalId = null;
      let hideFlyoutTimer = null;
      let totalPlansInManifest = 0;
      let completedPlansCounter = 0;
      const DECK0_TIMELINE_KEY = 'deck0_timeline_snapshots_v1';
      const tasksProgressUI = {
        root: null,
        badge: null,
        text: null,
        count: null,
        spinner: null,
        check: null,
        bar: null
      };

      let isDragSelecting = false;
      let dragSelectionState = true;
      // let zenEditorOverlay = null; // [MOD] Removed
      let ctxMenuElement = null;

      // Network Stream State


      // ================= DOM FACTORY (SVG-AWARE) =================
      const el = (tag, options = {}) => {
        const isSvg = ['svg', 'path', 'g', 'circle'].includes(tag);
        const d = isSvg ?
          document.createElementNS('http://www.w3.org/2000/svg', tag) :
          document.createElement(tag);

        if (options.cls) d.className = options.cls;
        if (options.id) d.id = options.id;
        if (options.text && !isSvg) d.textContent = options.text;
        if (options.style) Object.assign(d.style, options.style);
        if (options.attrs) {
          for (const [k, v] of Object.entries(options.attrs)) {
            d.setAttribute(k, v);
          }
        }
        if (options.click) d.addEventListener('click', options.click);
        if (options.change) d.addEventListener('change', options.change);
        if (options.mousedown) d.addEventListener('mousedown', options.mousedown);
        if (options.mouseenter) d.addEventListener('mouseenter', options.mouseenter);
        if (options.mouseleave) d.addEventListener('mouseleave', options.mouseleave);
        if (options.children && Array.isArray(options.children)) {
          options.children.forEach(child => child && d.appendChild(child));
        }
        if (options.parent) options.parent.appendChild(d);
        return d;
      };

      // ================= CSS =================
      const styleTag = document.createElement('style');
      styleTag.textContent = `
        #${APP_ID} { position: fixed; top: 0; right: 0; width: 100vw; height: 100vh; z-index: 2147483647; font-family: ${THEME.font}; color: ${THEME.textMain}; pointer-events: none; transition: opacity 0.3s ease; }
        #${APP_ID}.deck0-animating { transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1); }
        #${APP_ID}.minimized { width: 64px; height: 64px; top: 24px; left: auto; right: 24px; pointer-events: auto !important; }
        #${APP_ID}.minimized .deck0-grid { display: none; }
        #${APP_ID}.minimized #deck0-hud-capsule { opacity: 0; pointer-events: none; } /* [V18.0] Hide Capsule when minimized */
        #${APP_ID}.minimized .deck0-restore-icon { display: flex; }
        @keyframes deck0-breathing-gradient { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
        .deck0-restore-icon { display: none; width: 100%; height: 100%; justify-content: center; align-items: center; cursor: pointer; background: linear-gradient(270deg, #0a84ff, #bf5af2, #ff375f); background-size: 600% 600%; animation: deck0-breathing-gradient 3s ease infinite; backdrop-filter: blur(20px); border: none; border-radius: 18px; font-size: 24px; font-weight: 800; color: #fff; box-shadow: 0 4px 15px rgba(0,0,0,0.5); }
        /* [V18.0] UNIFIED GEOMETRY (Locked to UX specs) */
        .deck0-grid { width: 100%; height: 100%; display: grid; grid-template-columns: 225px 1fr 300px; grid-template-rows: 36px 1fr 200px; grid-template-areas: "header header header" "sidebar main right" "sidebar term right"; background: ${THEME.bg}; backdrop-filter: ${THEME.backdrop}; pointer-events: auto; }
        .deck0-header { grid-area: header; border-bottom: ${THEME.border}; display: flex; align-items: center; justify-content: space-between; padding: 0 16px; font-size: 12px; background: rgba(0,0,0,0.2); -webkit-app-region: drag; }
        .deck0-header-right { display: flex; align-items: center; gap: 16px; }
        .deck0-win-ctrl { display: flex; gap: 8px; align-items: center; }
        .deck0-win-btn { width: 12px; height: 12px; border-radius: 50%; cursor: pointer; }
        .deck0-settings-btn { width: 24px; height: 24px; border-radius: 4px; cursor: pointer; display: flex; align-items: center; justify-content: center; color: ${THEME.textDim}; transition: all 0.2s; }
        .deck0-settings-btn:hover { background: rgba(255,255,255,0.1); color: #fff; transform: rotate(90deg); }
        .deck0-sidebar { grid-area: sidebar; border-right: ${THEME.border}; background: rgba(0,0,0,0.3); display: flex; flex-direction: column; overflow: hidden; }
        .deck0-right { grid-area: right; border-left: ${THEME.border}; background: rgba(0,0,0,0.3); display: flex; flex-direction: column; overflow: hidden; }
        .deck0-term-area, .deck0-sub-term-area { display: flex; flex-direction: column; overflow: hidden; position: relative; }
        .deck0-term-area { grid-area: term; border-top: ${THEME.border}; background: rgba(0,0,0,0.3); }
        .deck0-sub-term-area { flex: 0 0 45%; border-top: ${THEME.border}; background: rgba(0,0,0,0.2); }
        .deck0-term-header, .deck0-sub-term-header { height: 24px; display: flex; border-bottom: ${THEME.border}; background: rgba(255,255,255,0.02); flex-shrink: 0; }
        .deck0-term-tab, .deck0-sub-term-tab { padding: 0 12px; height: 100%; display: flex; align-items: center; font-size: 10px; font-weight: 600; cursor: pointer; color: ${THEME.textDim}; transition: color 0.2s; border-right: ${THEME.border}; }
        .deck0-term-tab:hover, .deck0-sub-term-tab:hover:not(.disabled) { color: #fff; }
        .deck0-term-tab.active, .deck0-sub-term-tab.active { color: ${THEME.accent}; background: rgba(255,255,255,0.05); }
        .deck0-sub-term-tab.disabled { opacity: 0.4; cursor: not-allowed; }
        .deck0-term-content, .deck0-sub-term-content { flex: 1; overflow-y: auto; padding: 12px; font-family: ${THEME.fontMono}; font-size: 11px; color: #bbb; scroll-behavior: smooth; position: relative; }
        .deck0-stream-view { white-space: pre-wrap; line-height: 1.5; color: #e0e0e0; display: none; }
        .deck0-main { grid-area: main; display: flex; flex-direction: column; padding: 0; position: relative; overflow: hidden; background: #1e1e1e; }
        .deck0-editor-tabs { display: flex; overflow-x: auto; background: rgba(0,0,0,0.3); height: 35px; border-bottom: 1px solid #1e1e1e; flex-shrink: 0; }
        .deck0-editor-tab { display: flex; align-items: center; padding: 0 10px; min-width: 100px; max-width: 200px; height: 100%; background: transparent; color: #969696; font-size: 12px; cursor: pointer; border-right: 1px solid #1e1e1e; user-select: none; }
        .deck0-editor-tab.active { background: #1e1e1e; color: #fff; border-top: 1px solid ${THEME.accent}; }
	        .deck0-editor-tab:hover { background: #2a2d2e; }
	        .deck0-tab-close { margin-left: auto; width: 18px; height: 18px; border-radius: 3px; display: flex; align-items: center; justify-content: center; opacity: 0; transition: opacity 0.2s; }
	        .deck0-editor-tab:hover .deck0-tab-close { opacity: 1; }
	        .deck0-tab-close:hover { background: rgba(255,255,255,0.2); color: #fff; }
		        .deck0-editor-body { position: absolute; top: 35px; bottom: 0; left: 0; right: 0; background: #1e1e1e; overflow: auto; font-family: ${THEME.fontMono}; font-size: 13px; line-height: 1.5; }
		        .deck0-code-container { display: flex; min-height: 100%; }
		        .deck0-line-numbers { width: 40px; background: #1e1e1e; color: #666; text-align: right; padding-right: 10px; user-select: none; flex-shrink: 0; border-right: 1px solid #333; padding-top: 10px; }
			        .deck0-code-editor { position: relative; flex: 1; overflow: visible; }
			        .deck0-code-content { white-space: pre; color: #d4d4d4; tab-size: 4; font-family: ${THEME.fontMono}; font-size: 13px; line-height: 1.5; }
			        .deck0-code-content.deck0-code-highlight { display: block; position: relative; margin: 0; overflow: visible; padding: 10px 10px 10px 15px; pointer-events: none; }
				        #${APP_ID} .deck0-code-input { position: absolute; inset: 0; padding: 10px 10px 10px 15px; border: none; outline: none; resize: none; background: transparent !important; color: transparent !important; -webkit-text-fill-color: transparent !important; caret-color: #d4d4d4; font-family: ${THEME.fontMono}; font-size: 13px; line-height: 1.5; white-space: pre; overflow: hidden; tab-size: 4; }
				        #${APP_ID} .deck0-code-input[readonly] { caret-color: transparent; }

		        .deck0-mode-bar { height: 32px; display: flex; align-items: center; justify-content: space-between; padding: 0 10px; background: #121214; border-bottom: 1px solid rgba(255,255,255,0.08); pointer-events: auto; }
		        .deck0-mode-bar-left { display: flex; align-items: center; gap: 8px; min-width: 0; }
		        .deck0-mode-bar-title { font-size: 11px; color: #c5c5c5; font-weight: 800; letter-spacing: 0.3px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
		        .deck0-mode-bar-actions { display: flex; align-items: center; gap: 8px; }
		        .deck0-mode-btn { height: 22px; padding: 0 10px; border-radius: 6px; border: 1px solid rgba(255,255,255,0.10); background: rgba(255,255,255,0.06); color: #c5c5c5; cursor: pointer; font-size: 11px; font-weight: 800; display: inline-flex; align-items: center; justify-content: center; user-select: none; }
		        .deck0-mode-btn:hover { background: rgba(255,255,255,0.10); }
		        .deck0-mode-btn.primary { color: #fff; }

		        .deck0-tree-diff { margin: 0 0 0 6px; font-size: 11px; font-weight: 900; line-height: 1; }
		        .deck0-tree-diff.added { color: ${THEME.success}; }
		        .deck0-tree-diff.deleted { color: ${THEME.danger}; }
		        .deck0-tree-diff.modified { color: ${THEME.warning}; }
	        .deck0-empty-editor { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; color: #555; }
        .deck0-input-wrapper { flex: 1; display: flex; flex-direction: column; margin-bottom: 20px; }
        .deck0-input-area { width: 100%; height: 100%; background: transparent; border: none; outline: none; color: ${THEME.textMain}; font-family: ${THEME.fontMono}; font-size: 12px; line-height: 1.6; resize: none; }
        .deck0-action-bar { height: 48px; display: flex; gap: 10px; padding: 4px; background: rgba(255,255,255,0.03); border: ${THEME.border}; border-radius: 12px; }
        .deck0-btn { flex: 1; border: none; border-radius: 8px; font-size: 13px; font-weight: 500; cursor: pointer; color: #fff; transition: transform 0.1s; display: flex; align-items: center; justify-content: center; }
        .deck0-btn:active { transform: scale(0.97); }
        .btn-primary { background: ${THEME.accent}; }
        .btn-success { background: ${THEME.success}; }
        .btn-danger { background: ${THEME.danger}; }
        .deck0-list { padding: 10px; overflow-y: auto; flex: 1; user-select: none; min-height: 0; }
        .deck0-tree-item { padding: 4px 8px; font-size: 12px; color: ${THEME.textDim}; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; cursor: pointer; display: flex; align-items: center; gap: 8px; border-radius: 4px; }
        .deck0-tree-item:hover { color: #fff; background: rgba(255,255,255,0.05); }
        .deck0-tree-item.active-file { color: #ffffff; background: rgba(10, 132, 255, 0.15); border-left: 2px solid #0a84ff; padding-left: 6px !important; }
        .deck0-chat-actions { display: flex; align-items: center; gap: 10px; margin-top: 8px; padding-top: 8px; border-top: 1px solid rgba(255,255,255,0.1); }
        .deck0-chat-action-btn { cursor: pointer; width: 14px; height: 14px; color: #888; display: flex; align-items: center; justify-content: center; transition: color 0.2s; }
        .deck0-chat-action-btn:hover { color: #fff; }
        .deck0-tree-checkbox { width: 12px; height: 12px; border-radius: 2px; border: 1px solid rgba(255,255,255,0.3); background: transparent; appearance: none; cursor: pointer; position: relative; flex-shrink: 0; }
        .deck0-tree-checkbox:checked { background: ${THEME.accent}; border-color: ${THEME.accent}; }
        .deck0-tree-checkbox:checked::after { content: '✔'; position: absolute; top: -2px; left: 1px; font-size: 9px; color: #fff; }
        .deck0-change-card { background: rgba(255,255,255,0.03); border: ${THEME.border}; border-radius: 6px; margin-bottom: 6px; overflow: hidden; display: flex; height: 44px; transition: all 0.2s; position: relative; padding-right: 0; }
        .deck0-change-card:hover { background: rgba(255,255,255,0.06); }
        .deck0-card-actions { position: absolute; top: 0; right: 0; height: 100%; display: flex; align-items: center; gap: 4px; padding: 0 8px 0 20px; background: linear-gradient(90deg, transparent 0%, rgba(30, 30, 32, 0.95) 30%, rgba(30, 30, 32, 1) 100%); opacity: 0; pointer-events: none; transition: opacity 0.2s ease, transform 0.2s ease; transform: translateX(10px); }
        .deck0-change-card:hover .deck0-card-actions { opacity: 1; pointer-events: auto; transform: translateX(0); }
        .deck0-mini-action { width: 24px; height: 24px; border-radius: 4px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 10px; color: #fff; border: 1px solid rgba(255,255,255,0.1); background: rgba(255,255,255,0.05); transition: all 0.1s; }
        .deck0-mini-action:hover { transform: scale(1.1); }
        .deck0-btn-run:hover { background: rgba(48, 209, 88, 0.2); border-color: #30d158; color: #30d158; }
        .deck0-btn-undo:hover { background: rgba(255, 69, 58, 0.2); border-color: #ff453a; color: #ff453a; }
        .deck0-change-status { width: 4px; transition: background 0.3s; background: rgba(255,255,255,0.1); }
        .deck0-change-status.running { background: ${THEME.warning}; box-shadow: 0 0 8px ${THEME.warning}; animation: pulse 1s infinite; }
        .deck0-change-status.done { background: ${THEME.success}; box-shadow: none; }
        .deck0-change-content { flex: 1; display: flex; flex-direction: column; justify-content: center; padding: 0 12px; min-width: 0; }
        .deck0-tag { font-size: 9px; padding: 2px 5px; border-radius: 4px; font-weight: 700; margin-right: 6px; letter-spacing: 0.5px; display: inline-block; min-width: 36px; text-align: center; flex-shrink: 0; }
              /* Shadow Flyout */
              #deck0-shadow-flyout { position: absolute; width: 400px; background: rgba(0,0,0,0.3); backdrop-filter: blur(24px) saturate(180%); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 8px; padding: 15px; z-index: 10000; opacity: 0; pointer-events: none; transition: opacity 0.2s ease, transform 0.2s ease; transform: translateX(-10px); box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4); }
              #deck0-shadow-flyout.visible { opacity: 1; pointer-events: auto; transform: translateX(0); }
              .flyout-title { font-size: 14px; font-weight: 600; margin-bottom: 10px; display: flex; align-items: center; color: #fff; }
              .flyout-desc { font-size: 12px; color: #86868b; margin-bottom: 15px; border-left: 2px solid #0a84ff; padding-left: 10px; }
              .flyout-section-title { font-size: 10px; font-weight: bold; color: #86868b; text-transform: uppercase; margin-bottom: 8px; letter-spacing: 0.5px; }
              .flyout-file-list { font-family: "SF Mono", monospace; font-size: 11px; color: #bbb; }
              .flyout-file-item { margin-bottom: 4px; display: flex; align-items: center; }
            
        .deck0-change-title-row { display: flex; align-items: center; white-space: nowrap; overflow: hidden; }
        .deck0-change-title { font-size: 12px; font-weight: 500; color: #fff; display: flex; align-items: center; }
        .deck0-commit-type { font-weight: 700; margin-right: 4px; }
        .deck0-change-sub { font-size: 10px; color: ${THEME.textDim}; font-family: ${THEME.fontMono}; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 2px; }
        .exec-log-item { display: flex; align-items: center; margin-bottom: 8px; border: 1px solid rgba(255,255,255,0.1); border-radius: 6px; padding: 4px 8px; transition: background-color 0.2s; position: relative; min-height: 36px; }
        .exec-log-item:hover { background-color: rgba(255,255,255,0.05); }
        .exec-log-icon { flex-shrink: 0; width: 20px; text-align: center; color: ${THEME.grey}; }
        .exec-log-content { flex: 1; min-width: 0; }
        .exec-log-title { color: ${THEME.textMain}; font-weight: 500; font-size: 11px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .exec-log-desc { color: ${THEME.textDim}; font-size: 10px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .exec-log-actions { display: flex; gap: 6px; margin-left: auto; opacity: 0; transition: opacity 0.2s; pointer-events: none; }
        .exec-log-item:hover .exec-log-actions { opacity: 1; pointer-events: auto; }
	        .exec-log-action-btn { background: rgba(255,255,255,0.1); border: none; color: #fff; width: 24px; height: 24px; border-radius: 4px; cursor: pointer; font-size: 12px; display: flex; align-items: center; justify-content: center; }
	        .exec-log-action-btn:hover { background: rgba(255,255,255,0.2); }
	        
		        /* TIMELINE (Snapshot List) */
		        .deck0-timeline-item { position: relative; padding: 10px 10px 10px 28px; border-bottom: 1px solid rgba(255,255,255,0.06); cursor: pointer; }
		        .deck0-timeline-item:hover { background: rgba(255,255,255,0.04); }
                .deck0-timeline-item.viewing { background: rgba(10, 132, 255, 0.15); border-left: 2px solid #0a84ff; padding-left: 26px; }
		        .deck0-timeline-line { position: absolute; left: 16px; top: 0; bottom: 0; width: 1px; background: rgba(255,255,255,0.10); }
		        .deck0-timeline-dot { position: absolute; left: 12px; top: 14px; width: 9px; height: 9px; border-radius: 50%; background: #666; border: 2px solid rgba(30,30,30,0.9); }
		        .deck0-timeline-dot.active { background: ${THEME.success}; box-shadow: 0 0 10px rgba(48, 209, 88, 0.35); }
		        .deck0-timeline-content { display: flex; flex-direction: column; gap: 3px; min-width: 0; }
		        .deck0-timeline-title { font-size: 12px; color: #c5c5c5; font-weight: 500; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
		        .deck0-timeline-meta-row { display: flex; align-items: center; justify-content: space-between; position: relative; height: 24px; }
		        .deck0-timeline-meta-text { font-size: 10px; color: #777; font-weight: 700; letter-spacing: 0.4px; text-transform: uppercase; white-space: nowrap; }
		        .deck0-timeline-actions { display: flex; align-items: center; gap: 6px; opacity: 0; transition: opacity 0.2s; position: absolute; right: 0; top: 0; height: 100%; }
		        .deck0-timeline-item:hover .deck0-timeline-actions { opacity: 1; }
		        .deck0-timeline-action-btn { width: 18px; height: 18px; border-radius: 5px; border: 1px solid rgba(255,255,255,0.10); background: rgba(255,255,255,0.06); color: #858585; cursor: pointer; font-size: 10px; display: flex; align-items: center; justify-content: center; padding: 0; line-height: 1; }
		        .deck0-timeline-action-btn:hover { background: rgba(255,255,255,0.10); color: #c5c5c5; }
		        .deck0-timeline-action-btn.cloud:hover { background: rgba(10, 132, 255, 0.18); border-color: rgba(10, 132, 255, 0.35); color: #0a84ff; }
		        .deck0-timeline-action-btn.delete:hover { background: rgba(255, 69, 58, 0.15); border-color: rgba(255, 69, 58, 0.35); color: #ff453a; }
		        
		        /* === V15 MODAL & FLYOUT STYLES === */
        .deck0-modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); backdrop-filter: blur(5px); z-index: 2147483647; display: flex; align-items: center; justify-content: center; animation: fadeIn 0.2s; }
        .deck0-modal-win { width: 90%; height: 90%; background: #1e1e1e; border: 1px solid #333; border-radius: 12px; display: flex; flex-direction: column; box-shadow: 0 20px 50px rgba(0,0,0,0.5); overflow: hidden; }
        .deck0-modal-header { height: 50px; border-bottom: 1px solid #333; display: flex; align-items: center; justify-content: space-between; padding: 0 20px; background: #252526; flex-shrink: 0; }
        .deck0-modal-title { font-size: 14px; fontWeight: bold; color: #fff; }
        .deck0-modal-body { flex: 1; display: flex; overflow: hidden; }
        .deck0-diff-sidebar { width: 250px; background: #252526; border-right: 1px solid #333; overflow-y: auto; }
        
        /* DIFF SIDEBAR TIERED VIEW - REFINED */
        .deck0-diff-plan-header { padding: 2px 8px; background: rgba(9,50,1,0.19); border-bottom: 1px solid rgba(255,255,255,0.05); }
        .deck0-diff-plan-title-row { display: flex; align-items: center; gap: 6px; margin-bottom: 2px; width: 100%; }
        .deck0-diff-plan-id { font-size: 9px; font-weight: bold; padding: 2px 4px; border-radius: 3px; color: #fff; min-width: 32px; text-align: center; flex-shrink: 0; }
        .deck0-diff-plan-text { font-size: 11px; font-weight: 600; color: #eee; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; flex: 1; min-width: 0; }
        .deck0-diff-plan-desc { font-size: 9px; color: #777; padding-left: 2px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .deck0-diff-file-item { padding: 8px 12px; font-size: 11px; color: #ccc; cursor: pointer; border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; align-items: center; transition: background 0.1s; overflow: hidden; }
        .deck0-diff-file-item.deck0-nested { padding-left: 28px; border-left: 2px solid transparent; }
        .deck0-diff-file-item.active { background: #37373d; color: #fff; border-left-color: #12ff0a57; }
        .deck0-diff-file-item:hover { background: #2a2d2e; }
	        .deck0-file-path-text { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; flex: 1; min-width: 0; }

	        .deck0-diff-content { flex: 1; overflow: auto; background: #1e1e1e; padding: 20px; min-height: 0; }
	        .deck0-diff-content.deck0-diff-with-edit { overflow: hidden; padding: 0; }
	        .deck0-diff-scroll { flex: 1; overflow: auto; background: #1e1e1e; padding: 20px; min-height: 0; }
	        .deck0-diff-edit-panel { border-top: 1px solid #333; background: #161616; padding: 10px 12px; display: flex; flex-direction: column; gap: 8px; flex-shrink: 0; }
	        .deck0-diff-edit-header { display: flex; align-items: center; justify-content: space-between; gap: 10px; }
	        .deck0-diff-edit-title { font-size: 10px; color: #777; font-weight: 700; letter-spacing: 0.4px; text-transform: uppercase; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; flex: 1; min-width: 0; }
	        .deck0-diff-edit-hint { font-size: 10px; color: #666; font-family: ${THEME.fontMono}; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	        .deck0-diff-edit-textarea { width: 100%; min-height: 140px; max-height: 320px; resize: vertical; padding: 10px; border: 1px solid rgba(255,255,255,0.10); border-radius: 8px; background: rgba(0,0,0,0.25); color: #d4d4d4; font-family: ${THEME.fontMono}; font-size: 12px; line-height: 1.5; outline: none; }
	        .deck0-diff-view { font-family: ${THEME.fontMono}; font-size: 12px; line-height: 1.6; white-space: pre; }
	        .deck0-diff-line { display: flex; align-items: stretch; width: 100%; min-width: fit-content; }
        .deck0-hunk-nav { position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 1px; opacity: 0; visibility: hidden; transition: opacity 0.1s; }
        .deck0-line-is-hunk:hover .deck0-hunk-nav { visibility: visible; opacity: 0.6; }
        .deck0-line-is-hunk:hover .deck0-hunk-nav:hover { opacity: 1; }
        .deck0-nav-btn { width: 100%; height: 50%; background: #555; border: none; border-radius: 2px; color: #eee; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 10px; line-height: 1; }
        .deck0-diff-nums-container { display: flex; flex-shrink: 0; background-color: rgba(255,255,255,0.02); }
        .deck0-diff-num-old, .deck0-diff-num-new { width: 35px; text-align: right; color: #6e7681; user-select: none; padding: 0 5px; }
        .deck0-diff-add .deck0-diff-num-new { color: #a3e6b7; font-weight: 600; }
        .deck0-diff-del .deck0-diff-num-old { color: #f8ad9d; font-weight: 600; }
        .deck0-diff-op-container { width: 24px; text-align: center; flex-shrink: 0; position: relative; margin: 0 4px; }
        .deck0-op-symbol { transition: opacity 0.1s; font-weight: bold; user-select: none; }
        .deck0-diff-add .deck0-op-symbol { color: ${THEME.success}; }
        .deck0-diff-del .deck0-op-symbol { color: ${THEME.danger}; }
        .deck0-line-is-hunk:hover .deck0-op-symbol { opacity: 0; }
        .deck0-line-is-hunk.active-hunk .deck0-op-symbol { animation: blink 1s step-end infinite; }
        .deck0-diff-code { flex: 1; padding: 0 10px; }
        .deck0-diff-add { background: rgba(46, 160, 67, 0.15); }
        .deck0-diff-del { background: rgba(248, 81, 73, 0.15); }
        .deck0-modal-footer { height: 60px; border-top: 1px solid #333; display: flex; align-items: center; justify-content: flex-end; padding: 0 20px; gap: 10px; background: #252526; }
        #deck0-plan-details-flyout { position: absolute; width: 400px; background: ${THEME.panelBg}; backdrop-filter: ${THEME.backdrop}; border: ${THEME.border}; border-radius: 8px; padding: 15px; z-index: 10; opacity: 0; pointer-events: none; transition: opacity 0.2s ease, transform 0.2s ease; transform: translateX(-10px); box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4); }
        #deck0-plan-details-flyout.visible { opacity: 1; pointer-events: auto; transform: translateX(0); }
        .flyout-title { font-size: 14px; font-weight: 600; margin-bottom: 10px; display: flex; align-items: center; }
        .flyout-desc { font-size: 12px; color: ${THEME.textDim}; margin-bottom: 15px; border-left: 2px solid ${THEME.accent}; padding-left: 10px; }
        .flyout-section-title { font-size: 10px; font-weight: bold; color: ${THEME.textDim}; text-transform: uppercase; margin-bottom: 8px; letter-spacing: 0.5px; }
        .flyout-file-list { font-family: ${THEME.fontMono}; font-size: 11px; }
        .flyout-file-item { margin-bottom: 4px; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        @keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }
        /* THINKING: Purple <-> Yellow */
        @keyframes breathe-thinking { 0% { background-color: ${THEME.purple}; color: #fff; } 50% { background-color: ${THEME.warning}; color: #000; } 100% { background-color: ${THEME.purple}; color: #fff; } }
        /* GENERATING: Yellow <-> Green */
        @keyframes breathe-generating { 0% { background-color: ${THEME.warning}; color: #000; } 50% { background-color: ${THEME.success}; color: #fff; } 100% { background-color: ${THEME.warning}; color: #000; } }
        
        .deck0-badge-thinking { animation: breathe-thinking 2s infinite ease-in-out; transition: all 0.3s; }
        .deck0-badge-generating { animation: breathe-generating 1.5s infinite ease-in-out; transition: all 0.3s; }

        /* CONTEXT MENU */
        .deck0-ctx-menu { position: fixed; background: #252526; border: 1px solid #454545; box-shadow: 0 4px 12px rgba(0,0,0,0.5); border-radius: 5px; padding: 4px 0; min-width: 160px; z-index: 2147483647; display: none; flex-direction: column; }
        .deck0-ctx-item { padding: 6px 12px; font-size: 12px; color: #cccccc; cursor: pointer; display: flex; align-items: center; gap: 8px; transition: background 0.1s; }
        .deck0-ctx-item:hover { background: #094771; color: #fff; }
        .deck0-ctx-sep { height: 1px; background: #454545; margin: 4px 0; }
        .deck0-ctx-shortcut { margin-left: auto; color: #888; font-size: 10px; }
        
        .deck0-inline-input { background: #3c3c3c; border: 1px solid #007acc; color: #fff; font-family: inherit; font-size: 12px; padding: 2px 4px; border-radius: 2px; outline: none; width: 100%; box-sizing: border-box; }

        /* EXPERT SWITCHER */
        .deck0-expert-wrapper { position: absolute; top: -32px; left: 0; z-index: 10; font-family: ${THEME.font}; }
        .deck0-expert-badge { display: flex; align-items: center; gap: 6px; padding: 4px 10px; background: rgba(0,0,0,0.4); border: 1px solid rgba(255,255,255,0.1); border-radius: 6px 6px 0 0; cursor: pointer; color: #fff; font-size: 11px; font-weight: 600; transition: all 0.2s; border-bottom: none; backdrop-filter: blur(4px); }
        .deck0-expert-badge:hover { background: rgba(255,255,255,0.1); }
        .deck0-expert-menu { position: absolute; top: 100%; left: 0; width: 180px; background: #252526; border: 1px solid #454545; border-radius: 0 6px 6px 6px; display: none; flex-direction: column; overflow: hidden; box-shadow: 0 4px 12px rgba(0,0,0,0.5); }
        .deck0-expert-wrapper:hover .deck0-expert-menu { display: flex; }
        .deck0-expert-item { padding: 8px 12px; font-size: 11px; color: #ccc; cursor: pointer; display: flex; align-items: center; gap: 8px; border-left: 3px solid transparent; transition: background 0.1s; }
        .deck0-expert-item:hover { background: rgba(255,255,255,0.05); color: #fff; }
        .deck0-expert-item.active { background: rgba(255,255,255,0.08); color: #fff; }

        /* [V16.8] Plan Header Controls */
        .deck0-plan-ctrl-group { display: flex; align-items: center; gap: 0; margin-left: 0; padding: 2px 4px; border-radius: 12px; background: rgba(255,255,255,0.05); transition: background 0.2s; border: 1px solid rgba(255,255,255,0.05); }
        .deck0-plan-ctrl-group:hover { background: rgba(255,255,255,0.1); border-color: rgba(255,255,255,0.2); }

        .deck0-plan-btn { width: 20px; height: 20px; display: flex; align-items: center; justify-content: center; cursor: pointer; border-radius: 50%; transition: transform 0.2s; }
        .deck0-plan-btn:hover { transform: scale(1.15); background: rgba(255,255,255,0.1); }
        .deck0-plan-undo { width: 0; opacity: 0; pointer-events: none; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); overflow: hidden; margin-right: 0; }
        .deck0-plan-ctrl-group:hover .deck0-plan-undo { width: 20px; opacity: 1; pointer-events: auto; margin-right: 6px; }
        
        /* [V16.8] Plan Status Markers */
        .deck0-plan-marker { position: absolute; bottom: 6px; right: 6px; width: 16px; height: 16px; z-index: 5; pointer-events: none; opacity: 0; transform: scale(0.5); transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .deck0-plan-marker.visible { opacity: 1; transform: scale(1); }

	        /* [V17.9] Accordion & Compact Task List */
	        .deck0-accordion { border-top: 1px solid rgba(255,255,255,0.05); background: rgba(0,0,0,0.2); width: 100%; }
	        .deck0-accordion-header { padding: 6px 10px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; user-select: none; font-size: 10px; font-weight: 700; color: #888; transition: background 0.2s; height: 28px; }
	        .deck0-accordion-header:hover { background: rgba(255,255,255,0.05); color: #fff; }
	        .deck0-accordion-body { display: none; overflow-y: auto; max-height: 400px; background: rgba(0,0,0,0.1); border-bottom: 1px solid rgba(255,255,255,0.05); }
	        .deck0-accordion-body.open { display: block; }

	        /* Tasks Progress (Cline-like) */
	        .deck0-tasks-progress { margin: 1px 8px; border: 1px solid rgba(255,255,255,0.06); background: rgba(42,45,46,0.7); border-radius: 4px; overflow: hidden; }
	        .deck0-tasks-progress-row { display: flex; align-items: center; justify-content: space-between; gap: 10px; padding: 1px 10px; min-height: 22px; }
	        .deck0-tasks-progress-left { display: flex; align-items: center; gap: 8px; flex: 1; min-width: 0; }
	        .deck0-tasks-progress-badge { font-size: 11px; font-weight: 700; padding: 1px 4px; border-radius: 6px; background: rgba(255,255,255,0.1); color: #c5c5c5; flex-shrink: 0; }
	        .deck0-tasks-progress-text { font-size: 12px; font-weight: 600; color: #c5c5c5; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	        .deck0-tasks-progress-right { display: flex; align-items: center; gap: 8px; flex-shrink: 0; color: #858585; }
	        .deck0-tasks-progress-count { font-size: 10px; font-family: ${THEME.fontMono}; }
	        .deck0-spinner { width: 12px; height: 12px; border-radius: 50%; border: 2px solid rgba(255,255,255,0.18); border-top-color: #0a84ff; animation: deck0-spin 0.9s linear infinite; }
	        .deck0-tasks-progress-check { font-size: 12px; color: #30d158; font-weight: 900; }
	        .deck0-tasks-progress-underline { height: 3px; width: 0%; background: #30d158; transition: width 0.25s ease; opacity: 0.9; }
	        @keyframes deck0-spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }

	        /* Changed Files (Cline-like) */
	        .deck0-files-card { margin: 8px; border: 1px solid rgba(255,255,255,0.06); background: rgba(42,45,46,0.7); border-radius: 4px; overflow: hidden; }
	        .deck0-files-card-row { display: flex; align-items: center; justify-content: space-between; gap: 10px; padding: 8px 10px; }
	        .deck0-files-card-title { font-size: 12px; font-weight: 600; color: #c5c5c5; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	        .deck0-files-card-meta { display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
	        .deck0-files-card-stats { display: flex; align-items: center; gap: 6px; font-family: ${THEME.fontMono}; font-size: 11px; }
	        .deck0-files-stat-add { color: #30d158; }
	        .deck0-files-stat-del { color: #ff453a; }
	        .deck0-files-card-actions { display: flex; align-items: center; gap: 6px; }
	        .deck0-files-action-btn { padding: 2px 8px; border-radius: 4px; font-size: 11px; font-weight: 700; cursor: pointer; user-select: none; border: 1px solid rgba(255,255,255,0.10); background: rgba(255,255,255,0.08); color: #c5c5c5; transition: background 0.15s; }
	        .deck0-files-action-btn:hover { background: rgba(255,255,255,0.12); }
	        .deck0-files-action-btn.keep { background: rgba(10, 132, 255, 0.20); border-color: rgba(10, 132, 255, 0.35); color: #0a84ff; }
	        .deck0-files-action-btn.keep:hover { background: rgba(10, 132, 255, 0.28); }

	        .deck0-changed-file-row { display: flex; align-items: center; gap: 10px; padding: 6px 10px; border-top: 1px solid rgba(255,255,255,0.04); cursor: pointer; }
	        .deck0-changed-file-row:hover { background: rgba(255,255,255,0.05); }
	        .deck0-changed-file-row.deleted { opacity: 0.6; cursor: default; }
	        .deck0-changed-file-main { display: flex; align-items: center; gap: 8px; flex: 1; min-width: 0; }
	        .deck0-changed-file-icon { width: 14px; height: 14px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; opacity: 0.9; }
	        .deck0-changed-file-name { font-family: ${THEME.fontMono}; font-size: 11px; color: #c5c5c5; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	        .deck0-changed-file-dir { font-size: 10px; color: #858585; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	        .deck0-changed-file-right { display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
	        .deck0-changed-file-stats { display: flex; align-items: center; gap: 6px; font-family: ${THEME.fontMono}; font-size: 11px; }
	        .deck0-changed-file-actions { display: flex; align-items: center; gap: 4px; opacity: 0; transition: opacity 0.15s; }
	        .deck0-changed-file-row:hover .deck0-changed-file-actions { opacity: 1; }
	        .deck0-changed-file-row.deleted:hover .deck0-changed-file-actions { opacity: 0; }
	        
	        .deck0-task-row { display: flex; align-items: center; padding: 4px 10px; border-bottom: 1px solid rgba(255,255,255,0.03); cursor: pointer; transition: background 0.1s; position: relative; font-size: 11px; height: 28px; }
	        .deck0-task-row:hover { background: rgba(255,255,255,0.05); }
        .deck0-task-status-dot { width: 6px; height: 6px; border-radius: 50%; margin-right: 8px; background: #444; flex-shrink: 0; transition: all 0.3s; }
        .deck0-task-status-dot.pending { background: #555; }
        .deck0-task-status-dot.running { background: #ff9f0a; box-shadow: 0 0 5px #ff9f0a; animation: pulse 1s infinite; }
        .deck0-task-status-dot.done { background: #30d158; }
        
        .deck0-task-id { font-family: "SF Mono", monospace; color: #666; margin-right: 8px; font-size: 9px; min-width: 45px; }
        .deck0-task-title { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; color: #ccc; }
        .deck0-task-actions { display: flex; gap: 4px; opacity: 0; transition: opacity 0.2s; margin-left: 8px; }
        .deck0-task-row:hover .deck0-task-actions { opacity: 1; }
        
	        .deck0-btn-mini { width: 18px; height: 18px; border-radius: 3px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 10px; color: #fff; background: rgba(255,255,255,0.1); transition: all 0.1s; }
	        .deck0-btn-mini:hover { transform: scale(1.1); background: rgba(255,255,255,0.2); }
	        .deck0-btn-mini.run:hover { background: rgba(48, 209, 88, 0.3); color: #30d158; }
	        .deck0-btn-mini.keep:hover { background: rgba(10, 132, 255, 0.3); color: #0a84ff; }
	        .deck0-btn-mini.undo:hover { background: rgba(255, 69, 58, 0.3); color: #ff453a; }

        .deck0-file-row { padding: 4px 12px; font-size: 10px; color: #999; display: flex; align-items: center; font-family: "SF Mono", monospace; border-bottom: 1px solid rgba(255,255,255,0.02); }
        .deck0-file-row:hover { color: #fff; background: rgba(255,255,255,0.03); }
        
        /* [V18.0] Thinking & Reasoning Blocks */
        .deck0-thinking-container { margin-bottom: -14px; border-radius: 8px; background: rgba(30, 30, 32, 0.4); overflow: hidden; border: 1px solid rgba(255,255,255,0.08); transition: all 0.2s; flex-shrink: 0; max-width: 90%; align-self: flex-start; width: 100%; }
        .deck0-thinking-header { padding: 4px 8px; font-size: 10px; font-weight: 700; color: #999; cursor: pointer; display: flex; align-items: center; gap: 6px; user-select: none; transition: all 0.2s; background: rgba(255,255,255,0.04); min-height: 24px; }
        .deck0-thinking-header:hover { color: #fff; background: rgba(255,255,255,0.08); }
        .deck0-thinking-content { display: none; padding: 12px; font-family: "SF Mono", monospace; font-size: 12px; color: #bbb; line-height: 1.6; white-space: pre-wrap; border-top: 1px solid rgba(255,255,255,0.05); background: rgba(0,0,0,0.2); max-height: 400px; overflow-y: auto; scroll-behavior: smooth; }
        .deck0-thinking-content.open { display: block; animation: fadeIn 0.3s ease; }
        .deck0-thinking-arrow { font-size: 8px; transition: transform 0.2s; transform: rotate(-90deg); }
        .deck0-thinking-content.open ~ .deck0-thinking-header .deck0-thinking-arrow, .deck0-thinking-header.active .deck0-thinking-arrow { transform: rotate(0deg); }

        /* [V17.0 Parity] Hide underlying popups instead of fading legacy UI */
        body.deck0-automation-active .cdk-overlay-container,
        body.deck0-automation-active .mat-mdc-menu-panel,
        body.deck0-automation-active .mat-mdc-dialog-container { opacity: 0 !important; pointer-events: none !important; }

        /* [V18.1] Force Visible Scrollbars */
        .deck0-editor-body::-webkit-scrollbar,
        .deck0-list::-webkit-scrollbar,
        .deck0-term-content::-webkit-scrollbar,
        .deck0-chat-history::-webkit-scrollbar,
        .deck0-diff-content::-webkit-scrollbar,
        .deck0-diff-sidebar::-webkit-scrollbar,
        .deck0-input-area::-webkit-scrollbar,
        .deck0-accordion-body::-webkit-scrollbar { width: 12px; height: 12px; }

        .deck0-editor-body::-webkit-scrollbar-track,
        .deck0-list::-webkit-scrollbar-track,
        .deck0-term-content::-webkit-scrollbar-track,
        .deck0-chat-history::-webkit-scrollbar-track,
        .deck0-diff-content::-webkit-scrollbar-track,
        .deck0-diff-sidebar::-webkit-scrollbar-track,
        .deck0-input-area::-webkit-scrollbar-track,
        .deck0-accordion-body::-webkit-scrollbar-track { background: transparent; }

        .deck0-editor-body::-webkit-scrollbar-thumb,
        .deck0-list::-webkit-scrollbar-thumb,
        .deck0-term-content::-webkit-scrollbar-thumb,
        .deck0-chat-history::-webkit-scrollbar-thumb,
        .deck0-diff-content::-webkit-scrollbar-thumb,
        .deck0-diff-sidebar::-webkit-scrollbar-thumb,
        .deck0-input-area::-webkit-scrollbar-thumb,
        .deck0-accordion-body::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.15);
            border-radius: 6px;
            border: 3px solid transparent;
            background-clip: content-box;
        }

        .deck0-editor-body::-webkit-scrollbar-thumb:hover,
        .deck0-list::-webkit-scrollbar-thumb:hover,
        .deck0-term-content::-webkit-scrollbar-thumb:hover,
        .deck0-chat-history::-webkit-scrollbar-thumb:hover,
        .deck0-diff-content::-webkit-scrollbar-thumb:hover,
        .deck0-diff-sidebar::-webkit-scrollbar-thumb:hover,
        .deck0-input-area::-webkit-scrollbar-thumb:hover,
        .deck0-accordion-body::-webkit-scrollbar-thumb:hover {
            background: rgba(255, 255, 255, 0.3);
            border: 3px solid transparent;
            background-clip: content-box;
        }
	    `;
      // [TP-P4-1] Moved to Deck0UI.init() - do not execute here
      // document.head.appendChild(styleTag);

      // ================= UI & HELPERS =================
      // [TP-P4-1] Root element created immediately (needed by subsequent UI code)
      // CSS injection is deferred to Deck0UI.init()
      const root = el('div', {
        id: APP_ID,
        parent: document.body
      });

      // [V16.8] Smooth Minimize Helper (Prevents Resize Lag)
      const setMinimized = (state) => {
        root.classList.add('deck0-animating');
        // Force reflow
        void root.offsetWidth;
        if (state) root.classList.add('minimized');
        else root.classList.remove('minimized');
        Deck0Effects.clock.setTimeout(() => root.classList.remove('deck0-animating'), 350);
      };

      el('div', {
        cls: 'deck0-restore-icon',
        text: 'G',
        parent: root,
        click: () => setMinimized(false)
      });

      // [V18.0] UX Optimized Grid
      const grid = el('div', {
        cls: 'deck0-grid',
        parent: root
      });
      const header = el('div', {
        cls: 'deck0-header',
        style: {
          position: 'relative'
        },
        parent: grid
      });
      header.addEventListener('dblclick', () => setMinimized(true));
      el('div', {
        text: 'Deck 0 mini',
        style: {
          fontWeight: '700',
          letterSpacing: '0.5px',
          position: 'absolute',
          left: '50%',
          transform: 'translateX(-50%)',
          pointerEvents: 'none',
          userSelect: 'none'
        },
        parent: header
      });

      // --- FIXED HEADER LAYOUT ---
      const headerRight = el('div', {
        cls: 'deck0-header-right',
        style: {
          marginLeft: 'auto',
          zIndex: '10'
        },
        parent: header
      });

      const renderSettingsView = (container) => {
        // Stub overlay for compatibility
        const overlay = {
          remove: () => { }
        };

        container.replaceChildren();
        container.style.display = 'flex';
        container.style.height = '100%';
        container.style.background = '#1e1e1e';
        container.style.overflow = 'hidden';

        // --- SIDEBAR ---
        const sidebar = el('div', {
          style: {
            width: '220px',
            background: 'rgba(0,0,0,0.3)',
            borderRight: '1px solid #333',
            display: 'flex',
            flexDirection: 'column',
            paddingTop: '10px'
          },
          parent: container
        });

        el('div', {
          text: 'Settings',
          style: {
            padding: '10px 20px 15px',
            fontSize: '11px',
            fontWeight: 'bold',
            color: '#888',
            textTransform: 'uppercase',
            letterSpacing: '0.5px'
          },
          parent: sidebar
        });

        const contentArea = el('div', {
          style: {
            flex: '1',
            overflowY: 'auto',
            display: 'flex',
            flexDirection: 'column'
          },
          parent: container
        });

        // State
        let activeTab = 'General';
        const tabs = ['General', 'GitHub'];
        const tabMap = new Map();

        // Helpers
        const renderSection = (parent, title, desc, fn) => {
          const sec = el('div', {
            style: {
              marginBottom: '30px'
            },
            parent
          });
          el('div', {
            text: title,
            style: {
              fontSize: '14px',
              fontWeight: '500',
              color: '#fff',
              marginBottom: '6px'
            },
            parent: sec
          });
          if (desc) {
            el('div', {
              text: desc,
              style: {
                fontSize: '12px',
                color: '#888',
                marginBottom: '12px',
                lineHeight: '1.4',
                maxWidth: '500px'
              },
              parent: sec
            });
          }
          const controlWrap = el('div', {
            style: {
              maxWidth: '500px'
            },
            parent: sec
          });
          fn(controlWrap);
        };

        const renderGeneral = (parent) => {
          // 1. Storage Mode
          renderSection(parent, 'Storage Mode', 'Determines where file operations are executed and persisted.', (wrap) => {
            const row = el('div', {
              style: {
                display: 'flex',
                gap: '10px'
              },
              parent: wrap
            });

            const addModeCard = (mode, label, desc) => {
              const isActive = Deck0.store.getState().project.storageMode === mode;
              const card = el('div', {
                style: {
                  flex: 1,
                  padding: '10px',
                  border: `1px solid ${isActive ? THEME.accent : '#444'}`,
                  borderRadius: '4px',
                  cursor: 'pointer',
                  background: isActive ? 'rgba(10, 132, 255, 0.1)' : 'rgba(255,255,255,0.03)',
                  transition: 'all 0.2s'
                },
                parent: row,
                click: () => {
                  Deck0Actions.project.setStorageMode(mode);
                  updateStorageUI();
                  renderActiveContent(); // Redraw
                }
              });
              el('div', {
                text: label,
                style: {
                  fontSize: '12px',
                  fontWeight: 'bold',
                  color: isActive ? '#fff' : '#ccc',
                  marginBottom: '4px'
                },
                parent: card
              });
              el('div', {
                text: desc,
                style: {
                  fontSize: '10px',
                  color: '#888',
                  lineHeight: '1.3'
                },
                parent: card
              });
              // Action Button if active
              if (isActive) {
                const btnText = mode === 'LOCAL' ? '📂 Select Folder' : (mode === 'VIRTUAL' ? 'Initialize' : 'Test Connection');
                el('button', {
                  cls: 'deck0-btn',
                  text: btnText,
                  style: {
                    marginTop: '8px',
                    fontSize: '10px',
                    height: '24px',
                    background: '#333',
                    border: '1px solid #555'
                  },
                  parent: card,
                  click: async (e) => {
                    e.stopPropagation();
                    if (mode === 'LINK') {
                      try {
                        const res = await LinkAdapter.check();
                        alert(`Connected: ${res.cwd}`);
                        await Deck0.orchestrator.mount();
                      } catch (err) {
                        alert('Connection Failed: ' + err.message);
                      }
                    } else {
                      await Deck0.orchestrator.mount();
                    }
                  }
                });
              }
            };

            addModeCard('VIRTUAL', 'VIRTUAL (IndexedDB)', 'Browser cache. Offline.');
            addModeCard('LINK', 'RELAY (Node Server)', 'Sync writes to Relay workspace.');
            addModeCard('LOCAL', 'LOCAL DISK', 'Direct access (Chrome/Edge).');
          });

          // 2. Behavior
          renderSection(parent, 'Automation Behavior', 'Control how the assistant interacts with history and commits.', (wrap) => {
            const addCheck = (label, checked, onChange) => {
              const l = el('label', {
                style: {
                  display: 'flex',
                  alignItems: 'center',
                  gap: '8px',
                  marginBottom: '8px',
                  cursor: 'pointer',
                  fontSize: '12px',
                  color: '#ccc'
                },
                parent: wrap
              });
              const cb = el('input', {
                attrs: {
                  type: 'checkbox'
                },
                style: {
                  accentColor: THEME.accent
                },
                parent: l
              });
              cb.checked = checked;
              cb.onchange = () => onChange(cb.checked);
              el('span', {
                text: label,
                parent: l
              });
            };

            addCheck('Auto Clear Chat History', Deck0.store.getState().settings.clearChat, (v) => {
              Deck0Actions.settings.setClearChat(v);
            });
          });

          // 3. Audio
          renderSection(parent, 'Notification Sound', 'Play a sound when execution completes.', (wrap) => {
            const inp = el('input', {
              cls: 'deck0-input-area',
              style: {
                border: '1px solid #444',
                height: '32px',
                padding: '5px',
                borderRadius: '4px'
              },
              attrs: {
                placeholder: 'https://example.com/sound.mp3',
                value: Deck0.store.getState().settings.audioUrl
              },
              parent: wrap
            });
            inp.onchange = () => {
              Deck0Actions.settings.setAudioUrl(inp.value.trim());
            };
          });

          // 4. Cache
          renderSection(parent, 'System', 'Maintenance operations.', (wrap) => {
            // Export Button
            el('button', {
              cls: 'deck0-btn',
              text: '💾 Export Virtual Disk',
              style: {
                width: 'auto',
                padding: '0 12px',
                marginRight: '10px',
                background: '#333',
                border: '1px solid #555'
              },
              parent: wrap,
              click: async () => {
                try {
                  const keys = await IDBHelper.getAllKeys();
                  if (keys.length === 0) return alert('Virtual Disk is empty.');

                  const exportData = {
                    meta: {
                      timestamp: Date.now(),
                      source: 'Deck0_Mini_Virtual_Export',
                      version: '1.0'
                    },
                    files: {}
                  };

                  let count = 0;
                  for (const k of keys) {
                    const content = await IDBHelper.get(k);
                    exportData.files[k] = content;
                    count++;
                  }

                  const blob = new Blob([JSON.stringify(exportData, null, 2)], {
                    type: 'application/json'
                  });
                  const url = URL.createObjectURL(blob);
                  const a = document.createElement('a');
                  a.href = url;
                  a.download = `deck0-virtual-export-${Date.now()}.json`;
                  document.body.appendChild(a);
                  a.click();
                  document.body.removeChild(a);
                  URL.revokeObjectURL(url);

                  log(`✅ Exported ${count} files from Virtual Disk.`, 'success');
                } catch (e) {
                  log(`Export failed: ${e.message}`, 'error');
                }
              }
            });

            // Clear Button
            el('button', {
              cls: 'deck0-btn btn-danger',
              text: '🗑️ Clear Virtual Cache',
              style: {
                width: 'auto',
                padding: '0 12px'
              },
              parent: wrap,
              click: async () => {
                if (confirm('Wipe all Virtual Files?')) {
                  await IDBHelper.clear();
                  if (Deck0.store.getState().project.storageMode === 'VIRTUAL') refreshFileTree();
                  log('Virtual Disk Cleared', 'success');
                }
              }
            });
          });
        };

        const renderGitHub = (parent) => {
          renderSection(parent, 'Repository', 'Linked GitHub repository (owner/repo).', (wrap) => {
            const inp = el('input', {
              cls: 'deck0-input-area',
              style: {
                border: '1px solid #444',
                height: '32px',
                padding: '5px',
                borderRadius: '4px'
              },
              attrs: {
                placeholder: 'facebook/react',
                value: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.GH_REPO) || ''
              },
              parent: wrap
            });
            inp.onchange = () => Deck0Effects.persist.set(DECK0_STORAGE_KEYS.GH_REPO, inp.value.trim());
          });

          renderSection(parent, 'Access Token', 'Personal Access Token (PAT) with Repo scope.', (wrap) => {
            const grp = el('div', {
              style: {
                display: 'flex',
                gap: '8px'
              },
              parent: wrap
            });
            const inp = el('input', {
              cls: 'deck0-input-area',
              style: {
                border: '1px solid #444',
                height: '32px',
                padding: '5px',
                borderRadius: '4px',
                flex: 1
              },
              attrs: {
                type: 'password',
                placeholder: 'ghp_...',
                value: Deck0Effects.persist.get(DECK0_STORAGE_KEYS.GH_TOKEN) || ''
              },
              parent: grp
            });
            inp.onchange = () => Deck0Effects.persist.set(DECK0_STORAGE_KEYS.GH_TOKEN, inp.value.trim());

            el('div', {
              style: {
                width: '32px',
                height: '32px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                cursor: 'pointer',
                background: 'rgba(255,255,255,0.1)',
                borderRadius: '4px'
              },
              parent: grp,
              click: (e) => {
                const type = inp.type;
                inp.type = type === 'password' ? 'text' : 'password';
                e.currentTarget.style.color = type === 'password' ? THEME.accent : '#fff';
              },
              children: [
                el('div', {
                  text: '👁',
                  style: {
                    fontSize: '14px'
                  }
                })
              ]
            });
          });
        };

        const renderActiveContent = () => {
          contentArea.replaceChildren();

          // Header
          const header = el('div', {
            style: {
              padding: '20px 40px',
              borderBottom: '1px solid #333'
            },
            parent: contentArea
          });
          el('div', {
            text: activeTab,
            style: {
              fontSize: '18px',
              fontWeight: '500',
              color: '#fff'
            },
            parent: header
          });

          // Body
          const body = el('div', {
            style: {
              flex: 1,
              overflowY: 'auto',
              padding: '30px 40px'
            },
            parent: contentArea
          });

          if (activeTab === 'General') renderGeneral(body);
          else if (activeTab === 'GitHub') renderGitHub(body);
        };

        const renderSidebar = () => {
          sidebar.replaceChildren();
          el('div', {
            text: 'Settings',
            style: {
              padding: '10px 20px 15px',
              fontSize: '11px',
              fontWeight: 'bold',
              color: '#888',
              textTransform: 'uppercase',
              letterSpacing: '0.5px'
            },
            parent: sidebar
          });

          const createTab = (name) => {
            const isActive = activeTab === name;
            const item = el('div', {
              text: name,
              style: {
                padding: '8px 20px',
                cursor: 'pointer',
                fontSize: '13px',
                color: isActive ? '#fff' : '#ccc',
                background: isActive ? '#37373d' : 'transparent',
                borderLeft: isActive ? `3px solid ${THEME.accent}` : '3px solid transparent'
              },
              parent: sidebar,
              click: () => {
                activeTab = name;
                renderSidebar(); // Update Selection
                renderActiveContent();
              }
            });
            item.addEventListener('mouseenter', () => {
              if (activeTab !== name) item.style.background = '#2a2d2e';
            });
            item.addEventListener('mouseleave', () => {
              if (activeTab !== name) item.style.background = 'transparent';
            });
          };

          tabs.forEach(createTab);
        };

        renderSidebar();
        renderActiveContent();
        updateStorageUI();
      };

      // App Controls (Settings)
      const appCtrl = el('div', {
        cls: 'deck0-win-ctrl',
        parent: headerRight
      });

      // Global Shortcut
      document.addEventListener('keydown', (e) => {
        if (e.ctrlKey || e.metaKey) {
          const k = e.key.toLowerCase();
          if (k === 'e') {
            e.preventDefault();
            // Instant Minimize Toggle (No Animation)
            root.classList.toggle('minimized');
          }
        }
      });

      // Storage Indicator
      const storageInd = el('div', {
        id: DECK0_DOM_IDS.STORAGE_INDICATOR,
        text: 'LOCAL',
        style: {
          fontSize: '9px',
          fontWeight: 'bold',
          color: '#777',
          paddingRight: '10px',
          cursor: 'pointer'
        },
        parent: appCtrl,
        click: () => EditorManager.open('Settings')
      });

      el('div', {
        cls: 'deck0-settings-btn',
        parent: appCtrl,
        attrs: {
          title: 'Settings'
        },
        click: () => EditorManager.open('Settings'),
        children: [
          el('svg', {
            attrs: {
              viewBox: '0 0 16 16',
              width: '14',
              height: '14',
              fill: 'currentColor'
            },
            children: [
              el('path', {
                attrs: {
                  'd': 'M9.405 1.05c-.413-1.4-2.397-1.4-2.81 0l-.1.34a1.464 1.464 0 0 1-2.105.872l-.31-.17c-1.283-.698-2.686.705-1.987 1.987l.169.311c.446.82.023 1.841-.872 2.105l-.34.1c-1.4.413-1.4 2.397 0 2.81l.34.1a1.464 1.464 0 0 1 .872 2.105l-.17.31c-.698 1.283.705 2.686 1.987 1.987l.311-.169a1.464 1.464 0 0 1 2.105.872l.1.34c.413 1.4 2.397 1.4 2.81 0l.1-.34a1.464 1.464 0 0 1 2.105-.872l.31.17c1.283.698 2.686-.705 1.987-1.987l-.169-.311a1.464 1.464 0 0 1 .872-2.105l.34-.1c1.4-.413 1.4-2.397 0-2.81l-.34-.1a1.464 1.464 0 0 1-.872-2.105l.17-.31c.698-1.283-.705-2.686-1.987-1.987l-.311.169a1.464 1.464 0 0 1-2.105-.872l-.1-.34zM8 10.93a2.929 2.929 0 1 1 0-5.86 2.929 2.929 0 0 1 0 5.858z'
                }
              })
            ]
          })
        ]
      });

      // Window Controls (Min/Close)
      const winCtrl = el('div', {
        cls: 'deck0-win-ctrl',
        style: {
          paddingLeft: '8px',
          borderLeft: '1px solid rgba(255,255,255,0.1)'
        },
        parent: headerRight
      });
      el('div', {
        cls: 'deck0-win-btn',
        style: {
          background: '#febc2e'
        },
        parent: winCtrl,
        click: () => setMinimized(true)
      });
      el('div', {
        cls: 'deck0-win-btn',
        style: {
          background: '#ff5f57'
        },
        parent: winCtrl,
        click: () => {
          if (window.originalXHR) window.XMLHttpRequest = window.originalXHR;
          if (window.originalFetch) window.fetch = window.originalFetch;
          root.remove();
        }
      });

      const sidebar = el('div', {
        cls: 'deck0-sidebar',
        parent: grid
      });

      // [V15.9 UI] Explorer Header with Pull Control
      const explorerHeader = el('div', {
        cls: 'deck0-tree-item',
        style: {
          fontWeight: 'bold',
          padding: '5px 12px',
          justifyContent: 'space-between',
          position: 'relative',
          borderBottom: THEME.border // [MOD] Added separator
        },
        parent: sidebar
      });
      el('span', {
        text: 'EXPLORER',
        parent: explorerHeader
      });

      // Pull Button & Dropdown Container
      const pullContainer = el('div', {
        style: {
          position: 'relative',
          display: 'flex',
          alignItems: 'center',
          gap: '4px'
        },
        parent: explorerHeader
      });

      const pullBtn = el('div', {
        style: {
          padding: '4px',
          borderRadius: '4px',
          cursor: 'pointer',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          transition: 'background 0.2s'
        },
        attrs: {
          title: 'Pull from Remote'
        },
        parent: pullContainer,
        children: [
          el('svg', {
            attrs: {
              viewBox: '0 0 24 24',
              width: '16',
              height: '16',
              fill: 'currentColor'
            },
            children: [
              el('path', {
                attrs: {
                  'd': 'M19.35 10.04C18.67 6.59 15.64 4 12 4 9.11 4 6.6 5.64 5.35 8.04 2.34 8.36 0 10.91 0 14c0 3.31 2.69 6 6 6h13c2.76 0 5-2.24 5-5 0-2.64-2.05-4.78-4.65-4.96zM17 13l-5 5-5-5h3V9h4v4h3z'
                }
              })
            ]
          })
        ],
        mouseenter: (e) => {
          e.currentTarget.style.background = 'rgba(255,255,255,0.1)';
        },
        mouseleave: (e) => {
          e.currentTarget.style.background = 'transparent';
        }
      });

      // [FIX] Use Fixed Position attached to ROOT to escape sidebar backdrop-filter containing block (UX Optimization Mode Fix)
      const pullDropdown = el('div', {
        style: {
          position: 'fixed',
          width: '260px',
          maxHeight: '200px',
          overflowY: 'auto',
          background: '#252526',
          border: '1px solid #444',
          borderRadius: '4px',
          zIndex: '2147483647',
          boxShadow: '0 4px 12px rgba(0,0,0,0.5)',
          display: 'none',
          flexDirection: 'column',
          pointerEvents: 'auto'
        },
        parent: root // Changed from pullContainer to root
      });

      let pullHideTimer = null;
      const showPullMenu = async () => {
        if (pullHideTimer) Deck0Effects.clock.clearTimeout(pullHideTimer);

        // Calculate Position dynamically (Align with Sidebar)
        const sidebarRect = sidebar.getBoundingClientRect();
        const btnRect = pullBtn.getBoundingClientRect();

        pullDropdown.style.top = `${btnRect.bottom}px`;
        pullDropdown.style.left = `${sidebarRect.left}px`;
        pullDropdown.style.width = `${sidebarRect.width}px`;
        pullDropdown.style.display = 'flex';

        pullDropdown.replaceChildren(el('div', {
          text: 'Loading branches...',
          style: {
            padding: '8px',
            color: '#777',
            fontSize: '10px'
          }
        }));

        try {
          const branches = await GitLink.getBranches();
          pullDropdown.replaceChildren();
          if (branches.length === 0) {
            el('div', {
              text: 'No branches found',
              style: {
                padding: '8px',
                color: '#777',
                fontSize: '10px'
              },
              parent: pullDropdown
            });
            return;
          }

          el('div', {
            text: 'Select Branch to PULL:',
            style: {
              padding: '4px 8px',
              color: '#555',
              fontSize: '9px',
              fontWeight: 'bold',
              background: '#1e1e1e',
              borderBottom: '1px solid #333'
            },
            parent: pullDropdown
          });

          branches.forEach(b => {
            const item = el('div', {
              text: b,
              style: {
                padding: '6px 8px',
                cursor: 'pointer',
                fontSize: '11px',
                color: '#ccc',
                borderBottom: '1px solid #333'
              },
              parent: pullDropdown,
              mouseenter: (e) => {
                e.target.style.background = '#094771';
                e.target.style.color = '#fff';
              },
              mouseleave: (e) => {
                e.target.style.background = 'transparent';
                e.target.style.color = '#ccc';
              },
              click: async (e) => {
                e.stopPropagation();
                pullDropdown.style.display = 'none';
                if (confirm(`⚠️ PULL WARNING\n\nAre you sure you want to pull branch "${b}"?\nThis will OVERWRITE local files with the same name.`)) {
                  await GitLink.pullBranch(b);
                }
              }
            });
          });
        } catch (e) {
          pullDropdown.textContent = 'Error loading branches';
        }
      };

      pullBtn.addEventListener('mouseenter', showPullMenu);
      pullDropdown.addEventListener('mouseenter', () => {
        if (pullHideTimer) Deck0Effects.clock.clearTimeout(pullHideTimer);
      });

      const hideMenu = () => {
        pullHideTimer = Deck0Effects.clock.setTimeout(() => {
          pullDropdown.style.display = 'none';
        }, 300);
      };
      pullBtn.addEventListener('mouseleave', hideMenu);
      pullDropdown.addEventListener('mouseleave', hideMenu);

      const fileTree = el('div', {
        cls: 'deck0-list',
        parent: sidebar
      });

      // [V17.3] SESSION COMMITS PANEL (Left Sidebar)
      const sessionPanel = el('div', {
        cls: 'deck0-sidebar-session',
        style: {
          height: '50%',
          display: 'flex',
          flexDirection: 'column',
          borderTop: THEME.border,
          background: 'transparent',
          transition: 'height 0.3s cubic-bezier(0.25, 0.8, 0.25, 1)',
          flexShrink: 0,
          overflow: 'hidden'
        },
        parent: sidebar
      });

      const sessionHeader = el('div', {
        style: {
          padding: '8px 12px',
          borderBottom: THEME.border,
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'space-between',
          background: 'rgba(255,255,255,0.02)',
          cursor: 'pointer',
          userSelect: 'none',
          flexShrink: 0,
          height: '36px'
        },
        parent: sessionPanel,
        click: (e) => {
          // Toggle Logic
          if (sessionPanel.style.height === '36px') {
            sessionPanel.style.height = '50%';
            toggleIcon.style.transform = 'rotate(0deg)';
          } else {
            sessionPanel.style.height = '36px';
            toggleIcon.style.transform = 'rotate(-90deg)';
          }
        }
      });

      const sessionTitleBox = el('div', {
        style: {
          display: 'flex',
          alignItems: 'center',
          gap: '8px'
        },
        parent: sessionHeader
      });

      const timelineTitleEl = el('span', {
        text: 'TIMELINE (0)',
        style: {
          fontSize: '10px',
          fontWeight: 'bold',
          color: THEME.textDim,
          letterSpacing: '0.5px'
        },
        parent: sessionTitleBox
      });

      const toggleIcon = el('div', {
        text: '▼',
        style: {
          fontSize: '10px',
          transition: 'transform 0.3s'
        },
        parent: sessionTitleBox
      });

      // (TIMELINE panel is a snapshot list; no push/sync button here.)

      const executionLogsView = el('div', {
        id: 'deck0-execution-logs-view',
        style: {
          flex: 1,
          overflowY: 'auto',
          padding: '10px'
        },
        parent: sessionPanel
      });

      const updateStorageUI = () => {
        const ind = Deck0.ui.refs.storageIndicator;
        if (ind) {
          ind.textContent = Deck0.store.getState().project.storageMode;
          ind.style.color = Deck0.store.getState().project.storageMode === 'VIRTUAL' ? THEME.purple : (Deck0.store.getState().project.storageMode === 'LINK' ? THEME.success : '#777');
        }
      };

      document.addEventListener('mouseup', () => {
        isDragSelecting = false;
      });

      const main = el('div', {
        id: DECK0_DOM_IDS.MAIN,
        cls: 'deck0-main',
        parent: grid
      });
      // Editor Mode Bar (Time Travel / Diff Review)
      let editorModeBar = null;
      let editorModeTitle = null;
      let editorModeActions = null;
      let editorModePreviewBtn = null;
      let editorModePrimaryBtn = null;
      let editorModeSecondaryBtn = null;

      const updateEditorModeBar = () => {
        if (!editorModeBar) return;
        const active = !!(Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode);
        const view = Deck0.store.getState().legacy.DECK0_VIEW;
        editorModeBar.style.display = active ? 'flex' : 'none';
        if (!active) return;

        const titleLeft = view.mode === 'time_travel' ? 'Time Travel Mode' : 'Diff Review';
        const titleRight = view.mode === 'time_travel' ? 'Viewing History' : (view.preview ? 'Preview' : 'Edit');
        const title = `${titleLeft}  |  ${titleRight}`;
        if (editorModeTitle) editorModeTitle.textContent = title;

        // Preview toggle only for diff review
        if (editorModePreviewBtn) {
          const canToggle = view.mode === 'diff_review';
          editorModePreviewBtn.style.display = canToggle ? 'inline-flex' : 'none';
          // Button is an action toggle (show the target mode)
          editorModePreviewBtn.textContent = view.preview ? 'Edit' : 'Preview';
        }

        if (editorModePrimaryBtn) {
          const showPrimary = view.mode !== 'diff_review';
          editorModePrimaryBtn.style.display = showPrimary ? 'inline-flex' : 'none';
          if (showPrimary) {
            editorModePrimaryBtn.textContent = view.confirmText || (view.mode === 'time_travel' ? 'Restore this version' : 'Apply changes');
            const bg = view.confirmBg || (view.mode === 'time_travel' ? THEME.success : THEME.success);
            editorModePrimaryBtn.style.background = bg;
            editorModePrimaryBtn.style.borderColor = bg;
          }
        }
        if (editorModeSecondaryBtn) {
          editorModeSecondaryBtn.textContent = view.cancelText || (view.mode === 'time_travel' ? 'Exit Time Travel' : 'Exit');
        }
      };

      const renderDiffPreviewInEditor = (parent, path, baseContent, nextContent) => {
        const container = el('div', {
          cls: 'deck0-diff-preview-root',
          parent
        });
        const diffs = DiffEngine.compute(baseContent || '', nextContent || '');
        const diffViewContainer = el('div', {
          cls: 'deck0-diff-view',
          parent: container
        });

        let oldLine = 1;
        let newLine = 1;
        let hunkCounter = 0;
        let hunkElements = [];
        let currentHunkIndex = -1;

        const navigate = (newIndex) => {
          if (hunkElements.length === 0 || newIndex < 0 || newIndex >= hunkElements.length) return;
          if (currentHunkIndex !== -1 && hunkElements[currentHunkIndex]) hunkElements[currentHunkIndex].classList.remove('active-hunk');
          currentHunkIndex = newIndex;
          const targetElement = hunkElements[currentHunkIndex];
          targetElement.classList.add('active-hunk');
          targetElement.scrollIntoView({
            behavior: 'smooth',
            block: 'center'
          });
        };

        diffs.forEach((d) => {
          let oldNumText = '',
            newNumText = '',
            opText = '';
          let isHunk = false;
          const lineClass = `deck0-diff-${d.type}`;

          if (d.type === 'eq') {
            oldNumText = oldLine++;
            newNumText = newLine++;
          } else {
            isHunk = true;
            if (d.type === 'add') {
              newNumText = newLine++;
              opText = '+';
            } else if (d.type === 'del') {
              oldNumText = oldLine++;
              opText = '-';
            }
          }

          const lineEl = el('div', {
            cls: `deck0-diff-line ${lineClass} ${isHunk ? 'deck0-line-is-hunk' : ''}`,
            parent: diffViewContainer
          });
          lineEl.appendChild(el('div', {
            cls: 'deck0-diff-nums-container',
            children: [
              el('div', {
                cls: 'deck0-diff-num-old',
                text: oldNumText
              }),
              el('div', {
                cls: 'deck0-diff-num-new',
                text: newNumText
              })
            ]
          }));

          const opContainer = el('div', {
            cls: 'deck0-diff-op-container',
            parent: lineEl
          });
          el('span', {
            cls: 'deck0-op-symbol',
            text: opText,
            parent: opContainer
          });

          if (isHunk) {
            const currentIndex = hunkCounter;
            el('div', {
              cls: 'deck0-hunk-nav',
              parent: opContainer,
              children: [
                el('button', {
                  cls: 'deck0-nav-btn',
                  text: '▲',
                  click: (e) => {
                    e.stopPropagation();
                    navigate(currentIndex - 1);
                  }
                }),
                el('button', {
                  cls: 'deck0-nav-btn',
                  text: '▼',
                  click: (e) => {
                    e.stopPropagation();
                    navigate(currentIndex + 1);
                  }
                })
              ]
            });
            hunkElements.push(lineEl);
            hunkCounter++;
          }

          const codeCell = el('div', {
            cls: 'deck0-diff-code'
          });
          const ext = path ? path.split('.').pop() : 'txt';
          const tokens = (typeof TitanHighlighter !== 'undefined') ?
            TitanHighlighter.tokenize(d.content || ' ', ext) :
            [{
              part: d.content || ' ',
              color: null
            }];
          tokens.forEach(t => el('span', {
            text: t.part,
            style: t.color ? {
              color: t.color
            } : {},
            parent: codeCell
          }));
          lineEl.appendChild(codeCell);
        });

        Deck0Effects.clock.setTimeout(() => navigate(0), 20);
      };

      // [V18.5] Explorer Highlight Helper
      const highlightExplorerFile = (path) => {
        if (!fileTree) return;
        const prev = fileTree.querySelectorAll(DECK0_SELECTORS.UI_ACTIVE_FILE);
        prev.forEach(el => el.classList.remove('active-file'));
        if (!path) return;
        const safePath = path.replace(/\\/g, '\\\\').replace(/"/g, '\\"');
        const el = fileTree.querySelector(`.deck0-tree-item[data-path="${safePath}"]`);
        if (el) el.classList.add('active-file');
      };

      // [V17.1] VS Code-like Editor Manager (Editable + Highlight Overlay)
      const EditorManager = {
        files: [],
        active: null,
        tabsEl: null,
        bodyEl: null,
        _saveTimers: new Map(), // path -> timer

        init: () => {
          // [TP-P3-3] Explicit dependencies (Passive Consumer Pattern)
          const { el, refs, log } = window.Deck0.ui;
          const mainContainer = refs.get('MAIN');

          editorModeBar = el('div', {
            cls: 'deck0-mode-bar',
            parent: mainContainer,
            style: {
              display: 'none'
            }
          });
          const left = el('div', {
            cls: 'deck0-mode-bar-left',
            parent: editorModeBar
          });
          editorModeTitle = el('div', {
            cls: 'deck0-mode-bar-title',
            text: '',
            parent: left
          });
          editorModeActions = el('div', {
            cls: 'deck0-mode-bar-actions',
            parent: editorModeBar
          });

          editorModePreviewBtn = el('button', {
            cls: 'deck0-mode-btn',
            text: 'Preview',
            parent: editorModeActions,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              if (!Deck0.store.getState().legacy.DECK0_VIEW || Deck0.store.getState().legacy.DECK0_VIEW.mode !== 'diff_review') return;
              Deck0Actions.legacy.togglePreview();
              updateEditorModeBar();
              if (EditorManager.active) await EditorManager.open(EditorManager.active);
            }
          });

          editorModePrimaryBtn = el('button', {
            cls: 'deck0-mode-btn primary',
            text: 'Apply',
            parent: editorModeActions,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              if (!Deck0.store.getState().legacy.DECK0_VIEW || !Deck0.store.getState().legacy.DECK0_VIEW.mode) return;
              if (typeof Deck0.store.getState().legacy.DECK0_VIEW.onConfirm === 'function') await Deck0.store.getState().legacy.DECK0_VIEW.onConfirm();
            }
          });

          editorModeSecondaryBtn = el('button', {
            cls: 'deck0-mode-btn',
            text: 'Exit',
            parent: editorModeActions,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              if (!Deck0.store.getState().legacy.DECK0_VIEW || !Deck0.store.getState().legacy.DECK0_VIEW.mode) return;
              if (typeof Deck0.store.getState().legacy.DECK0_VIEW.onCancel === 'function') await Deck0.store.getState().legacy.DECK0_VIEW.onCancel();
            }
          });

          EditorManager.tabsEl = el('div', {
            cls: 'deck0-editor-tabs',
            parent: mainContainer
          });
          EditorManager.bodyEl = el('div', {
            cls: 'deck0-editor-body',
            parent: mainContainer
          });
          EditorManager.renderEmpty();
        },

        _getEntry: (path) => (EditorManager.files || []).find(f => f && f.path === path) || null,

        _renderHighlight: (codeEl, content, ext) => {
          // [TP-P3-3] Explicit dependencies
          const TitanHighlighter = window.Deck0.core.highlight;
          const { el } = window.Deck0.ui;

          if (!codeEl) return;
          codeEl.replaceChildren();
          if (TitanHighlighter) {
            const tokens = TitanHighlighter.tokenize(String(content || ''), ext || 'txt');
            tokens.forEach(t => el('span', {
              text: t.part,
              style: t.color ? {
                color: t.color
              } : {},
              parent: codeEl
            }));
          } else {
            codeEl.textContent = String(content || '');
          }
        },

        _renderLineNumbers: (gutterEl, content) => {
          if (!gutterEl) return;
          const lines = String(content || '').split(/\r?\n/);
          gutterEl.replaceChildren();
          for (let i = 1; i <= Math.max(1, lines.length); i++) {
            el('div', {
              text: String(i),
              style: {
                lineHeight: '1.5',
                paddingRight: '10px'
              },
              parent: gutterEl
            });
          }
        },

        _scheduleAutoSave: (path, getContent) => {
          if (!path) return;
          // Never auto-save while in time travel view mode
          if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode === 'time_travel') return;

          const prev = EditorManager._saveTimers.get(path);
          if (prev) Deck0Effects.clock.clearTimeout(prev);
          const t = Deck0Effects.clock.setTimeout(async () => {
            try {
              const content = String(getContent ? getContent() : '');
              let ok = false;
              try {
                await VfsManager.write(path, content);
                ok = true;
              } catch (e) {
                ok = false;
                throw e;
              }
              if (ok) log(`💾 Saved: ${path}`, 'success');
            } catch (e) {
              log(`Save failed: ${path} - ${e.message}`, 'error');
            }
          }, 600);
          EditorManager._saveTimers.set(path, t);
        },

        open: async (path) => {
          // [TP-P3-3] Explicit dependencies
          const { normalizeVfsPath } = window.Deck0.core.path;
          const VfsManager = window.Deck0.vfs;
          const { log } = window.Deck0.ui;
          const view = Deck0.store.getState().legacy.DECK0_VIEW;

          const p = normalizeVfsPath(path);
          if (!p) return;

          let entry = EditorManager._getEntry(p);
          if (!entry) {
            entry = {
              path: p,
              content: ''
            };
            EditorManager.files.push(entry);
          }

          // Load content based on current mode
          entry._mode = 'code';
          entry._readOnly = false;
          entry._base = null;
          entry._next = null;

          if (view && view.mode) {
            const hasBase = !!(view.baseMap && view.baseMap.has(p));
            const hasNext = !!(view.virtualMap && view.virtualMap.has(p));
            const hasAny = hasBase || hasNext;

            // If this path isn't part of the current view maps, fall back to disk content (but respect readOnly).
            if (!hasAny) {
              entry._mode = 'code';
              entry._readOnly = !!view.readOnly;
              try {
                entry.content = await VfsManager.read(p);
                // [V18.6 Fix] Lazy Baseline Capture for Manual Edits
                // If we open a file and have no record of it in the current session snapshot,
                // assume the disk content AT OPEN TIME is the baseline.
                // [TP-P3-3] Use Action instead of direct mutation
                const normP = normalizeVfsPath(p);
                const contentToSnap = entry.content || '';
                window.Deck0.actions.project.ensureBaselineEntry(normP, contentToSnap);
                log(`[DEBUG] Lazy Baseline Captured: ${normP} (Len: ${contentToSnap.length})`, 'purple');
              } catch (e) {
                return log(`Cannot open ${p}: ${e.message}`, 'error');
              }
            } else {
              const status = view.statusMap && view.statusMap.get(p) ? view.statusMap.get(p) : 'unchanged';
              const base = hasBase ? view.baseMap.get(p) : null;
              const nextHas = hasNext;
              const next = nextHas ? view.virtualMap.get(p) : base;

              const readOnly = !!view.readOnly;
              if (view.preview && status !== 'unchanged') {
                entry._mode = 'diff';
                entry._readOnly = true;
                entry._base = (base === null) ? '' : String(base || '');
                entry._next = (next === null) ? '' : String(next || '');
              } else {
                entry._mode = 'code';
                entry._readOnly = readOnly;
                // In edit mode, show next content when available; if deleted, start from base (do not mutate virtualMap)
                const shown = (nextHas && next !== null) ? String(next || '') : (base === null ? '' : String(base || ''));
                entry.content = shown;
              }
            }
          } else {
            if (p === 'Settings') {
              entry._mode = 'settings';
              entry._readOnly = true;
              entry.content = '';
            } else {
              try {
                entry.content = await VfsManager.read(p);
              } catch (e) {
                return log(`Cannot open ${p}: ${e.message}`, 'error');
              }
            }
          }

          EditorManager.active = p;
          updateEditorModeBar();
          EditorManager.render();
        },

        refreshOpenFiles: async () => {
          // Only refresh from disk when not in a virtual view mode
          if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode) return;
          const entries = Array.isArray(EditorManager.files) ? EditorManager.files.filter(Boolean) : [];
          if (entries.length === 0) return;

          const remove = new Set();
          for (const entry of entries) {
            const p = normalizeVfsPath(entry.path);
            if (!p) {
              remove.add(entry.path);
              continue;
            }
            try {
              entry.content = await VfsManager.read(p);
              entry._mode = 'code';
              entry._readOnly = false;
              entry._base = null;
              entry._next = null;
            } catch (e) {
              // File may have been deleted/renamed while it was open
              remove.add(p);
            }
          }

          if (remove.size > 0) {
            EditorManager.files = entries.filter(f => f && !remove.has(normalizeVfsPath(f.path)));
            if (EditorManager.active && remove.has(normalizeVfsPath(EditorManager.active))) {
              EditorManager.active = EditorManager.files.length > 0 ? EditorManager.files[0].path : null;
            }
          }

          EditorManager.render();
        },

        close: (path, e) => {
          if (e) e.stopPropagation();
          const p = normalizeVfsPath(path);
          const idx = EditorManager.files.findIndex(f => f && f.path === p);
          if (idx === -1) return;
          EditorManager.files.splice(idx, 1);
          if (EditorManager.active === p) {
            EditorManager.active = EditorManager.files.length > 0 ? EditorManager.files[Math.min(idx, EditorManager.files.length - 1)].path : null;
          }
          EditorManager.render();
        },

        render: () => {
          // [TP-P3-3] Explicit dependencies
          const { el } = window.Deck0.ui;

          EditorManager.tabsEl.replaceChildren();
          EditorManager.bodyEl.replaceChildren();

          highlightExplorerFile(EditorManager.active);
          if (!EditorManager.active) return EditorManager.renderEmpty();

          // Tabs
          EditorManager.files.forEach(f => {
            if (!f) return;
            const isActive = f.path === EditorManager.active;
            const tab = el('div', {
              cls: `deck0-editor-tab ${isActive ? 'active' : ''}`,
              parent: EditorManager.tabsEl,
              click: () => {
                EditorManager.active = f.path;
                EditorManager.render();
              }
            });
            el('span', {
              text: '📄',
              style: {
                marginRight: '6px',
                fontSize: '10px'
              },
              parent: tab
            });
            el('span', {
              text: (f.path || '').split('/').pop(),
              style: {
                whiteSpace: 'nowrap',
                overflow: 'hidden',
                textOverflow: 'ellipsis'
              },
              parent: tab
            });
            el('div', {
              cls: 'deck0-tab-close',
              text: '×',
              parent: tab,
              click: (e) => EditorManager.close(f.path, e)
            });
          });

          const file = EditorManager._getEntry(EditorManager.active);
          if (!file) return;

          if (file._mode === 'diff') {
            const wrap = el('div', {
              style: {
                height: '100%',
                overflow: 'auto'
              },
              parent: EditorManager.bodyEl
            });
            renderDiffPreviewInEditor(wrap, file.path, String(file._base || ''), String(file._next || ''));
            return;
          }

          if (file._mode === 'settings' || file.path === 'Settings') {
            renderSettingsView(EditorManager.bodyEl);
            return;
          }

          // Code editor (editable)
          const container = el('div', {
            cls: 'deck0-code-container',
            parent: EditorManager.bodyEl
          });
          const gutter = el('div', {
            cls: 'deck0-line-numbers',
            parent: container
          });
          const codeWrap = el('div', {
            cls: 'deck0-code-editor',
            parent: container,
            click: (e) => {
              // [V18.15 UX] Event Delegation for Empty Area Focus
              if (e.target.nodeName !== 'TEXTAREA') {
                const ta = codeWrap.querySelector('textarea');
                if (ta) ta.focus();
              }
            }
          });
          const highlight = el('pre', {
            cls: 'deck0-code-content deck0-code-highlight',
            parent: codeWrap
          });
          const input = el('textarea', {
            cls: 'deck0-code-input',
            parent: codeWrap,
            attrs: file._readOnly ? {
              readonly: 'true',
              spellcheck: 'false'
            } : {
              spellcheck: 'false'
            }
          });

          const ext = (file.path || '').split('.').pop();
          input.value = String(file.content || '');
          EditorManager._renderLineNumbers(gutter, input.value);
          EditorManager._renderHighlight(highlight, input.value, ext);

          input.addEventListener('keydown', (e) => {
            if (file._readOnly) return;
            if (e.key === 'Tab') {
              e.preventDefault();
              const start = input.selectionStart;
              const end = input.selectionEnd;
              const val = input.value;
              input.value = val.slice(0, start) + '\t' + val.slice(end);
              input.selectionStart = input.selectionEnd = start + 1;
              input.dispatchEvent(new Event('input'));
            }
          });

          input.addEventListener('input', () => {
            if (file._readOnly) return;
            file.content = input.value;
            EditorManager._renderLineNumbers(gutter, input.value);
            EditorManager._renderHighlight(highlight, input.value, ext);

            const p = file.path;
            if (p) Deck0Actions.session.addSyncPath(p);
            try {
              renderChangedFiles();
            } catch (e) { }

            // In diff review edit mode: update virtual map (no disk write)
            if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode === 'diff_review' && !Deck0.store.getState().legacy.DECK0_VIEW.preview && Deck0.store.getState().legacy.DECK0_VIEW.virtualMap) {
              Deck0Actions.legacy.setVirtualContent(p, String(file.content || ''));
            }
            EditorManager._scheduleAutoSave(p, () => String(file.content || ''));
          });
        },

        renderEmpty: () => {
          const empty = el('div', {
            cls: 'deck0-empty-editor',
            parent: EditorManager.bodyEl
          });
          el('div', {
            text: 'Deck 0 Mini EDITOR',
            style: {
              fontSize: '20px',
              fontWeight: 'bold',
              marginBottom: '10px',
              color: '#333'
            },
            parent: empty
          });
          el('div', {
            text: 'Select a file to view',
            style: {
              color: '#555',
              fontSize: '12px'
            },
            parent: empty
          });
        }
      };

      // [V17.6 UI] Input Area (Moved to Right Panel later)
      const inputArea = el('textarea', {
        cls: 'deck0-input-area',
        style: {
          background: 'rgba(0,0,0,0.3)',
          border: '1px solid rgba(255,255,255,0.1)',
          borderRadius: '6px',
          padding: '12px 12px 45px 12px', // [MODIFIED] Bottom padding for buttons
          color: '#eee',
          resize: 'none',
          height: '100px',
          minHeight: '100px',
          maxHeight: '290px',
          overflowY: 'hidden',
          fontSize: '13px',
          lineHeight: '1.5',
          outline: 'none',
          marginBottom: '0',
          width: '100%',
          boxSizing: 'border-box'
        },
        attrs: {
          placeholder: EXPERTS['ARCHITECT'].desc
        }
        // Parent assigned later (Right Panel)
      });

      // Auto-Resize Logic (Max 290px)
      inputArea.addEventListener('input', function () {
        this.style.height = 'auto';
        const newHeight = Math.min(this.scrollHeight, 290);
        this.style.height = (newHeight < 60 ? 60 : newHeight) + 'px';
        this.style.overflowY = newHeight >= 290 ? 'auto' : 'hidden';
      });

      const rightPanel = el('div', {
        cls: 'deck0-right',
        parent: grid
      });
      const rightHeader = el('div', {
        cls: 'deck0-tree-item',
        style: {
          fontWeight: 'bold',
          padding: '5px 12px',
          justifyContent: 'space-between',
          display: 'flex',
          alignItems: 'center',
          borderBottom: THEME.border // [MOD] Added separator
        },
        parent: rightPanel
      });
      el('span', {
        text: 'CODE ASSISTANT',
        parent: rightHeader
      });

      // [V16.8] Header Controls Container
      const headerRightGroup = el('div', {
        style: {
          display: 'flex',
          alignItems: 'center',
          gap: '8px'
        },
        parent: rightHeader
      });

      // [V17.9 UI] Chat History (Replaces Spacer)
      const chatHistory = el('div', {
        cls: 'deck0-chat-history',
        style: {
          flex: '1',
          minHeight: '0', // [FIX] Prevent flex expansion
          overflowY: 'auto',
          padding: '10px',
          display: 'flex',
          flexDirection: 'column',
          gap: '24px'
        },
        parent: rightPanel
      });

      const addChatMessage = (role, text) => {
        const isUser = role === 'user';
        const bubble = el('div', {
          style: {
            alignSelf: isUser ? 'flex-end' : 'flex-start',
            background: isUser ? 'rgba(10, 132, 255, 0.2)' : 'rgba(255, 255, 255, 0.05)',
            border: isUser ? '1px solid rgba(10, 132, 255, 0.3)' : '1px solid rgba(255, 255, 255, 0.1)',
            borderRadius: '8px',
            padding: '8px 12px',
            maxWidth: '90%',
            fontSize: '12px',
            color: '#eee',
            whiteSpace: 'pre-wrap',
            wordBreak: 'break-word',
            overflowWrap: 'anywhere',
            lineHeight: '1.65'
          },
          parent: chatHistory
        });

        if (!isUser) {
          bubble.classList.add('deck0-assistant-bubble');
        }

        // [V18.7 Fix] Separate content container to protect actions from SmoothTyper overwrite
        const contentEl = el('div', {
          cls: 'deck0-msg-content',
          parent: bubble
        });
        contentEl.textContent = text;

        if (!isUser) {
          const actions = el('div', {
            cls: 'deck0-chat-actions',
            parent: bubble
          });

          // Retry Icon
          el('div', {
            cls: 'deck0-chat-action-btn',
            attrs: { title: 'Retry (Undo & Edit)' },
            parent: actions,
            click: async () => {
              // [TRANSITION] Use Shadow UI's retry handler
              if (Deck0.ui.shadow?.handleRetry) {
                await Deck0.ui.shadow.handleRetry();
                return;
              }
              if (confirm('Retry? This will undo changes and let you edit the prompt.')) {
                // 0. Robustness: Capture User Prompt from DOM (Context-Aware)
                let textToRestore = Deck0.store.getState().session.lastUserInstruction;
                try {
                  const prev = bubble.previousElementSibling;
                  if (prev) {
                    const contentEl = prev.querySelector(DECK0_SELECTORS.UI_CHAT_MESSAGE_CONTENT);
                    if (contentEl && contentEl.textContent) textToRestore = contentEl.textContent;
                  }
                } catch (e) { console.warn('Retry Text Capture Error', e); }

                // 1. Undo Changes
                if (SnapshotManager.currentConversationId) {
                  await SnapshotManager.undoConversation();
                }
                Deck0Actions.session.resetPendingChanges();
                renderChangedFiles();

                // 2. Clear Chat
                chatHistory.replaceChildren();

                // 3. Restore Input for Editing (No Auto-Run)
                const activeInput = document.querySelector(DECK0_SELECTORS.UI_CHAT_INPUT) || inputArea;
                if (activeInput && textToRestore) {
                  activeInput.value = textToRestore;
                  activeInput.dispatchEvent(new Event('input', { bubbles: true }));
                  activeInput.focus();
                  Deck0Actions.session.setLastUserInstruction(textToRestore);
                }
              }
            },
            children: [
              el('svg', {
                attrs: { viewBox: '0 0 24 24', width: '14', height: '14', fill: 'currentColor' },
                children: [el('path', { attrs: { d: 'M17.65 6.35C16.2 4.9 14.21 4 12 4c-4.42 0-7.99 3.58-7.99 8s3.57 8 7.99 8c3.73 0 6.84-2.55 7.73-6h-2.08c-.82 2.33-3.04 4-5.65 4-3.31 0-6-2.69-6-6s2.69-6 6-6c1.66 0 3.14.69 4.22 1.78L13 11h7V4l-2.35 2.35z' } })]
              })
            ]
          });

          // Thumbs Up
          el('div', {
            cls: 'deck0-chat-action-btn',
            parent: actions,
            children: [
              el('svg', {
                attrs: { viewBox: '0 0 24 24', width: '14', height: '14', fill: 'currentColor' },
                children: [el('path', { attrs: { d: 'M1 21h4V9H1v12zm22-11c0-1.1-.9-2-2-2h-6.31l.95-4.57.03-.32c0-.41-.17-.79-.44-1.06L14.17 1 7.59 7.59C7.22 7.95 7 8.45 7 9v10c0 1.1.9 2 2 2h9c.83 0 1.54-.5 1.84-1.22l3.02-7.05c.09-.23.14-.47.14-.73v-1.91l-.01-.01L23 10z' } })]
              })
            ]
          });

          // Thumbs Down
          el('div', {
            cls: 'deck0-chat-action-btn',
            parent: actions,
            children: [
              el('svg', {
                attrs: { viewBox: '0 0 24 24', width: '14', height: '14', fill: 'currentColor' },
                children: [el('path', { attrs: { d: 'M15 3H6c-.83 0-1.54.5-1.84 1.22l-3.02 7.05c-.09.23-.14.47-.14.73v1.91l.01.01L1 14c0 1.1.9 2 2 2h6.31l-.95 4.57-.03.32c0 .41.17.79.44 1.06L9.83 23l6.59-6.59c.36-.36.58-.86.58-1.41V5c0-1.1-.9-2-2-2zm4 0v12h4V3h-4z' } })]
              })
            ]
          });
        }

        chatHistory.scrollTop = chatHistory.scrollHeight;
        return bubble;
      };

      // [V18.3] Smooth Typer Engine (UX Optimization)
      const SmoothTyper = {
        queue: [],
        timer: null,
        elem: null,
        reset(elem) {
          // Flush pending queue to old element before switching
          if (this.elem && this.elem !== elem && this.queue.length > 0) {
            this.elem.textContent += this.queue.join('');
            this.queue = [];
          }
          this.elem = elem;
          if (this.timer) {
            cancelAnimationFrame(this.timer);
            this.timer = null;
          }
        },
        push(chunk, targetElem = null) {
          if (targetElem && this.elem !== targetElem) {
            this.reset(targetElem);
          }
          if (!chunk || !this.elem) return;
          for (const char of chunk) this.queue.push(char);
          if (!this.timer) this.tick();
        },
        tick() {
          if (!this.elem || this.queue.length === 0) {
            this.timer = null;
            return;
          }
          // Adaptive speed: Smooth but responsive (Silky)
          const len = this.queue.length;
          // [Fix] Smooth pacing: 2 chars min, scale with length but cap at 15 to avoid "instant" feel
          const chunk = Math.max(1, Math.min(15, Math.ceil(len / 3)));

          const chars = this.queue.splice(0, chunk).join('');
          this.elem.textContent += chars;

          // [V18.8 Fix] 1. Scroll the content element itself (Thinking Bubble Internal Scroll)
          // Always keep the internal view at the bottom to see the "typing"
          if (this.elem.scrollHeight > this.elem.clientHeight) {
            this.elem.scrollTop = this.elem.scrollHeight;
          }

          // [V18.15 Fix] 2. Robust Stick-to-Bottom for Main Chat
          // Solves "burst content" pushing view out of sync.
          let parent = this.elem.parentElement;
          while (parent && !parent.classList.contains('deck0-chat-history')) {
            parent = parent.parentElement;
          }
          if (parent) {
            // Force scroll to bottom to keep up with rapid expansion
            parent.scrollTop = parent.scrollHeight;
          }

          this.timer = requestAnimationFrame(() => this.tick());
        }
      };

      // [V17.9 UI] Tasks Accordion (Moved Up)
      const tasksAccordion = el('div', {
        cls: 'deck0-accordion',
        parent: rightPanel
      });
      const tasksHeader = el('div', {
        cls: 'deck0-accordion-header',
        parent: tasksAccordion
      });
      const tasksTitle = el('div', {
        style: {
          display: 'flex',
          alignItems: 'center',
          gap: '6px'
        },
        parent: tasksHeader
      });
      el('span', {
        text: '▼',
        cls: 'deck0-acc-arrow',
        style: {
          fontSize: '8px',
          transform: 'rotate(-90deg)',
          transition: 'transform 0.2s'
        },
        parent: tasksTitle
      });
      const tasksCount = el('span', {
        text: 'TASKS (0)',
        parent: tasksTitle
      });

      const tasksActions = el('div', {
        style: {
          display: 'flex',
          gap: '4px'
        },
        parent: tasksHeader
      });

      // Global Run
      el('div', {
        cls: 'deck0-btn-mini run',
        text: '▶',
        attrs: {
          title: 'Execute All'
        },
        parent: tasksActions,
        click: (e) => {
          e.stopPropagation();
          executeChanges();
        }
      });
      // Global Keep (Accept)
      el('div', {
        cls: 'deck0-btn-mini keep',
        text: '✓',
        attrs: {
          title: 'KEEP ALL'
        },
        parent: tasksActions,
        click: (e) => {
          e.stopPropagation();
          keepAllChanges();
        }
      });
      // Global Undo
      el('div', {
        cls: 'deck0-btn-mini undo',
        text: '↺',
        attrs: {
          title: 'Undo All'
        },
        parent: tasksActions,
        click: (e) => {
          e.stopPropagation();
          undoChanges();
        }
      });

      const tasksBody = el('div', {
        cls: 'deck0-accordion-body',
        id: DECK0_DOM_IDS.TASKS_BODY,
        parent: tasksAccordion
      });

      tasksHeader.onclick = (e) => {
        if (e.target.closest(DECK0_SELECTORS.UI_BTN_MINI)) return;
        const isOpen = tasksBody.classList.toggle('open');
        tasksAccordion.querySelector(DECK0_SELECTORS.UI_ACC_ARROW).style.transform = isOpen ? 'rotate(0deg)' : 'rotate(-90deg)';
      };

      // [V17.9 UI] Changed Files Accordion
      const filesAccordion = el('div', {
        cls: 'deck0-accordion',
        parent: rightPanel
      });
      const filesHeader = el('div', {
        cls: 'deck0-accordion-header',
        parent: filesAccordion
      });
      const filesTitle = el('div', {
        style: {
          display: 'flex',
          alignItems: 'center',
          gap: '6px'
        },
        parent: filesHeader
      });
      el('span', {
        text: '▼',
        cls: 'deck0-acc-arrow',
        style: {
          fontSize: '8px',
          transform: 'rotate(-90deg)',
          transition: 'transform 0.2s'
        },
        parent: filesTitle
      });
      const filesCount = el('span', {
        text: 'CHANGED FILES (0)',
        parent: filesTitle
      });
      const filesBody = el('div', {
        cls: 'deck0-accordion-body',
        id: DECK0_DOM_IDS.FILES_BODY,
        parent: filesAccordion
      });

      filesHeader.onclick = () => {
        const isOpen = filesBody.classList.toggle('open');
        filesAccordion.querySelector(DECK0_SELECTORS.UI_ACC_ARROW).style.transform = isOpen ? 'rotate(0deg)' : 'rotate(-90deg)';
      };

      // [V17.6 UI] Right Panel Chat Input
      const chatContainer = el('div', {
        cls: 'deck0-chat-container',
        style: {
          padding: '10px',
          marginTop: 'auto',
          position: 'relative',
          flexShrink: 0
        },
        parent: rightPanel
      });

      // Append the floating inputArea here
      chatContainer.appendChild(inputArea);

      // [V17.7] Input Controls

      // 1. Bottom-Left Group (Expert & Model)
      const bottomLeftGroup = el('div', {
        style: {
          position: 'absolute',
          bottom: '18px',
          left: '16px',
          zIndex: 10,
          display: 'flex',
          gap: '8px',
          alignItems: 'center'
        },
        parent: chatContainer
      });

      // Expert Button
      const expertBtn = el('div', {
        text: 'Expert',
        style: {
          fontSize: '9px',
          fontWeight: 'bold',
          color: THEME.accent,
          background: 'rgba(255,255,255,0.05)',
          padding: '2px 6px',
          borderRadius: '4px',
          cursor: 'pointer',
          border: '1px solid rgba(255,255,255,0.1)',
          userSelect: 'none',
          transition: 'all 0.2s'
        },
        parent: bottomLeftGroup,
        click: (e) => {
          e.stopPropagation();
          expertMenu.style.display = expertMenu.style.display === 'none' ? 'flex' : 'none';
          modelDropdown.style.display = 'none';
        }
      });

      // Expert Menu
      const expertMenu = el('div', {
        style: {
          position: 'absolute',
          bottom: '36px',
          left: '16px',
          width: '160px',
          background: '#252526',
          border: '1px solid #454545',
          borderRadius: '4px',
          display: 'none',
          flexDirection: 'column',
          zIndex: 20,
          boxShadow: '0 4px 12px rgba(0,0,0,0.5)'
        },
        parent: chatContainer,
        mouseleave: () => expertMenu.style.display = 'none'
      });

      // Model Button
      const autoBtn = el('div', {
        text: 'Model',
        style: {
          fontSize: '9px',
          fontWeight: 'bold',
          color: THEME.accent,
          background: 'rgba(255,255,255,0.05)',
          padding: '2px 6px',
          borderRadius: '4px',
          cursor: 'pointer',
          border: '1px solid rgba(255,255,255,0.1)',
          userSelect: 'none',
          transition: 'all 0.2s'
        },
        parent: bottomLeftGroup,
        click: (e) => {
          e.stopPropagation();
          modelDropdown.style.display = modelDropdown.style.display === 'none' ? 'flex' : 'none';
          expertMenu.style.display = 'none';
        }
      });

      // Model Dropdown
      const modelDropdown = el('div', {
        style: {
          position: 'absolute',
          bottom: '36px',
          left: '80px',
          width: '180px',
          background: '#252526',
          border: '1px solid #454545',
          borderRadius: '4px',
          display: 'none',
          flexDirection: 'column',
          zIndex: 20,
          boxShadow: '0 4px 12px rgba(0,0,0,0.5)'
        },
        parent: chatContainer,
        mouseleave: () => modelDropdown.style.display = 'none'
      });

      const models = ['gemini-3-pro-preview', 'gemini-3-flash-preview'];
      models.forEach(m => {
        el('div', {
          text: m,
          style: {
            padding: '6px 10px',
            fontSize: '10px',
            color: '#ccc',
            cursor: 'pointer',
            borderBottom: '1px solid rgba(255,255,255,0.05)'
          },
          parent: modelDropdown,
          mouseenter: (e) => {
            e.target.style.background = THEME.accent;
            e.target.style.color = '#fff';
          },
          mouseleave: (e) => {
            e.target.style.background = 'transparent';
            e.target.style.color = '#ccc';
          },
          click: () => {
            Deck0Actions.settings.setModel(m);
            autoBtn.title = m;
            modelDropdown.style.display = 'none';
            log(`Model set to: ${m}`, 'info');
          }
        });
      });

      // Send Icon (Assigned to variable for color sync)
      let updateSendBtnState = null; // Exposed for runPipeline/XHR

      const sendBtn = el('div', {
        style: {
          position: 'absolute',
          bottom: '18px',
          right: '16px',
          cursor: 'pointer',
          color: THEME.accent,
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          width: '26px',
          height: '26px',
          borderRadius: '4px',
          background: 'rgba(255,255,255,0.05)',
          transition: 'all 0.2s'
        },
        mouseenter: (e) => e.currentTarget.style.background = 'rgba(255,255,255,0.1)',
        mouseleave: (e) => e.currentTarget.style.background = 'rgba(255,255,255,0.05)',
        click: () => {
          if (Deck0.store.getState().runtime.isPipelineRunning) {
            // STOP Logic
            // [V18.5] Robust Stop Button Locator (Google AI Studio)
            // Strategy: Check for 'Stop' in aria-label OR exact text in child spans (ignoring aria-label="Run" mismatch)
            const stopBtn = Deck0Effects.dom.findStopButton();

            if (stopBtn) {
              stopBtn.click();
              log('🛑 Stop signal sent', 'warn');
            } else {
              log('⚠️ Stop button not found in UI', 'warn');
            }
            if (updateSendBtnState) updateSendBtnState(false);
          } else {
            Deck0.orchestrator.run();
          }
        },
        parent: chatContainer,
        children: [
          // Default Send Icon
          el('svg', {
            id: DECK0_DOM_IDS.SEND_ICON_SVG,
            attrs: {
              viewBox: '0 0 24 24',
              width: '14',
              height: '14',
              fill: 'currentColor'
            },
            children: [
              el('path', { attrs: { d: 'M2.01 21L23 12 2.01 3 2 10l15 2-15 2z' } })
            ]
          })
        ]
      });

      updateSendBtnState = (running) => {
        Deck0Actions.runtime.setPipelineRunning(running);
        const svg = Deck0.ui.refs.sendIconSvg;
        if (!svg) return;

        svg.replaceChildren();
        if (running) {
          // STOP Icon (Square)
          const rect = document.createElementNS('http://www.w3.org/2000/svg', 'path');
          rect.setAttribute('d', 'M6 6h12v12H6z'); // Simple square
          svg.appendChild(rect);
        } else {
          // SEND Icon
          const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
          path.setAttribute('d', 'M2.01 21L23 12 2.01 3 2 10l15 2-15 2z');
          svg.appendChild(path);
        }
      };

      // [V17.8] Expert Logic & Color Sync
      const setExpert = (id) => {
        const exp = EXPERTS[id];
        if (!exp) return;
        Deck0.store.mutate('settings', st => { st.expertId = id; });

        // 1. Update Expert Button
        expertBtn.textContent = exp.name;
        expertBtn.style.color = exp.color;
        expertBtn.style.borderColor = exp.color; // Sync Border

        // 2. Update Model Button
        autoBtn.style.color = exp.color;
        autoBtn.style.borderColor = exp.color; // Sync Border

        // 3. Update Send Button
        sendBtn.style.color = exp.color;

        // 4. Update Input Area Border
        inputArea.style.borderColor = exp.color;
        inputArea.placeholder = exp.desc;

        // 5. Update Menu Active State
        Array.from(expertMenu.children).forEach(child => {
          if (child.dataset.id === id) {
            child.style.background = 'rgba(255,255,255,0.1)';
            child.style.borderLeft = `3px solid ${exp.color}`;
            child.style.color = '#fff';
          } else {
            child.style.background = 'transparent';
            child.style.borderLeft = '3px solid transparent';
            child.style.color = '#ccc';
          }
        });
      };

      // Build Expert Menu
      Object.values(EXPERTS).forEach(exp => {
        const item = el('div', {
          cls: 'deck0-expert-item',
          attrs: {
            'data-id': exp.id
          },
          parent: expertMenu,
          click: (e) => {
            e.stopPropagation();
            setExpert(exp.id);
            expertMenu.style.display = 'none';
          }
        });
        el('div', {
          style: {
            width: '8px',
            height: '8px',
            borderRadius: '50%',
            background: exp.color
          },
          parent: item
        });
        el('span', {
          text: exp.name,
          parent: item
        });
      });

      // Init Default

      // Helper to generate rich batch messages
      const generateBatchCommitMsg = (filesCount) => {
        const _sel = Deck0.store.getState().session.executionLog; if (!_sel || _sel.length === 0) {
          return `feat: Manual Session Update (${filesCount} files)\n\nChanges made directly via Editor.`;
        }

        let msg = `feat: Session Batch Update (${Deck0.store.getState().session.executionLog.length} Plans)\n\nExecuted Plans:\n`;
        Deck0.store.getState().session.executionLog.forEach(item => {
          msg += `- [${item.id}] ${item.title}\n  ${item.desc}\n`;
        });
        return msg;
      };

      const termArea = el('div', {
        cls: 'deck0-term-area',
        parent: grid
      });
      const termHeader = el('div', {
        cls: 'deck0-term-header',
        parent: termArea
      });
      const tabLogs = el('div', {
        cls: 'deck0-term-tab active',
        text: 'SYSTEM LOGS',
        parent: termHeader,
        click: () => switchTermTab('logs')
      });
      const tabStream = el('div', {
        cls: 'deck0-term-tab',
        text: 'RAW STREAM',
        parent: termHeader,
        click: () => switchTermTab('stream')
      });

      const termContent = el('div', {
        cls: 'deck0-term-content',
        parent: termArea
      });
      const termLogs = el('div', {
        id: 'deck0-logs-view',
        parent: termContent
      });
      const termStream = el('div', {
        id: DECK0_DOM_IDS.STREAM_VIEW,
        cls: 'deck0-stream-view',
        parent: termContent
      });

      const setBadgeState = (text, color) => {
        // Badge logic removed
      };

      const switchTermTab = (tab) => {
        Deck0.store.mutate('ui', u => { u.activeTermTab = tab; });
        if (tab === 'logs') {
          tabLogs.classList.add('active');
          tabStream.classList.remove('active');
          termLogs.style.display = 'block';
          termStream.style.display = 'none';
        } else {
          tabLogs.classList.remove('active');
          tabStream.classList.add('active');
          termLogs.style.display = 'none';
          termStream.style.display = 'block';
        }
        termContent.scrollTop = termContent.scrollHeight;
      };

      const log = (msg, type = 'info', highlight = null) => {
        const color = type === 'error' ? THEME.danger : type === 'success' ? THEME.success : type === 'warn' ? THEME.warning : type === 'purple' ? THEME.purple : THEME.accent;

        // 1. Term Log
        const line = el('div', {
          style: {
            marginBottom: '4px',
            borderLeft: `2px solid ${color}`,
            paddingLeft: '6px'
          }
        });
        el('span', {
          text: `[${new Date().toLocaleTimeString()}] `,
          style: {
            color: '#555'
          },
          parent: line
        });
        if (highlight) el('span', {
          text: highlight.text,
          style: {
            color: highlight.color,
            fontWeight: 'bold',
            marginRight: '6px'
          },
          parent: line
        });
        el('span', {
          text: msg,
          style: {
            color: color
          },
          parent: line
        });

        termLogs.appendChild(line);
        if (Deck0.store.getState().ui.activeTermTab === 'logs') termContent.scrollTop = termContent.scrollHeight;
      };

      const loadTimelineSnapshots = () => {
        try {
          const arr = Deck0Effects.persist.jsonGet(DECK0_TIMELINE_KEY, []);
          Deck0.store.mutate('timeline', t => { t.snapshots = Array.isArray(arr) ? arr : []; });
        } catch (e) {
          Deck0.store.mutate('timeline', t => { t.snapshots = []; });
        }
      };

      const saveTimelineSnapshots = () => {
        try {
          // Keep latest 200 to avoid unbounded growth
          const all = Array.isArray(Deck0.store.getState().timeline.snapshots) ? Deck0.store.getState().timeline.snapshots : [];
          const overflowCount = Math.max(0, all.length - 200);
          const dropped = overflowCount > 0 ? all.slice(0, overflowCount) : [];
          const next = all.slice(-200);
          Deck0.store.mutate('timeline', t => { t.snapshots = next; });
          Deck0Effects.persist.jsonSet(DECK0_TIMELINE_KEY, next);

          // Best-effort prune snapshot payloads
          dropped.forEach(s => {
            try {
              TimelineDB.delete(s.id).catch(() => { });
            } catch (e) { }
          });
        } catch (e) { }
      };

      const timeAgo = (timestamp) => {
        const ts = Number(timestamp) || 0;
        if (!ts) return '';
        const delta = Math.max(0, Date.now() - ts);
        const min = Math.floor(delta / 60000);
        if (min < 1) return 'JUST NOW';
        if (min < 60) return `${min}M AGO`;
        const hr = Math.floor(min / 60);
        if (hr < 24) return `${hr}H AGO`;
        const day = Math.floor(hr / 24);
        return `${day}D AGO`;
      };

      // [TP-P4-1] Deck0UI: Lifecycle wrapper for UI side effects
      const Deck0UI = {
        _initialized: false,

        init() {
          if (this._initialized) {
            console.warn('[Deck0UI] Already initialized, skipping');
            return;
          }

          // Inject CSS (deferred to avoid side effects at script load)
          if (!document.getElementById('deck0-styles')) {
            styleTag.id = 'deck0-styles';
            document.head.appendChild(styleTag);
          }

          this._initialized = true;
          console.log('[Deck0UI] Initialized: CSS injected');
        }
      };

      // ===================== DECK0 STORAGE =====================

      const LocalVfs = {
        read: async (path) => {
          try {
            const parts = path.split('/');
            const name = parts.pop();
            const dir = await getDir(Deck0.store.getState().project.dirHandle, parts.join('/'), false);
            const fh = await dir.getFileHandle(name);
            const f = await fh.getFile();
            return await f.text();
          } catch (e) {
            const err = new Error(`ENOENT: ${path}`);
            err.code = 'ENOENT';
            throw err;
          }
        },
        write: async (path, content) => {
          const parts = path.split('/');
          const name = parts.pop();
          const dir = await getDir(Deck0.store.getState().project.dirHandle, parts.join('/'), true);
          const fh = await dir.getFileHandle(name, {
            create: true
          });
          const w = await fh.createWritable();
          await w.write(content);
          await w.close();
        },
        del: async (path) => {
          try {
            const parts = path.split('/');
            const name = parts.pop();
            const dir = await getDir(Deck0.store.getState().project.dirHandle, parts.join('/'), false);
            await dir.removeEntry(name, {
              recursive: true
            });
          } catch (e) {
            // Ignore if already missing
          }
        },
        list: async () => {
          const paths = [];
          const traverse = async (dir, prefix = '') => {
            for await (const entry of dir.values()) {
              const path = prefix ? `${prefix}/${entry.name}` : entry.name;
              if (entry.kind === 'file') paths.push(path);
              else if (entry.kind === 'directory') await traverse(entry, path);
            }
          };
          if (Deck0.store.getState().project.dirHandle) await traverse(Deck0.store.getState().project.dirHandle);
          return paths;
        }
      };

      const VirtualVfs = {
        read: async (path) => {
          const val = await IDBHelper.get(path);
          if (val === undefined) {
            const err = new Error(`ENOENT: ${path}`);
            err.code = 'ENOENT';
            throw err;
          }
          return val;
        },
        write: async (path, content) => {
          await IDBHelper.put(path, content);
        },
        del: async (path) => {
          await IDBHelper.delete(path);
        },
        list: async () => {
          return await IDBHelper.getAllKeys();
        }
      };

      const LinkVfs = {
        read: async (path) => {
          try {
            return await LinkAdapter.read(path);
          } catch (e) {
            const err = new Error(`ENOENT: ${path}`);
            err.code = 'ENOENT';
            throw err;
          }
        },
        write: async (path, content) => {
          await LinkAdapter.write(path, content);
        },
        del: async (path) => {
          await LinkAdapter.delete(path);
        },
        list: async () => {
          return await LinkAdapter.getTree();
        },
        reveal: async (path) => {
          await LinkAdapter.reveal(path);
        }
      };

      // [V19.0 CORE] VFS Manager (T2-2)

      const VfsManager = {
        get driver() {
          const mode = store.getState().project.storageMode;
          if (mode === 'VIRTUAL') return VirtualVfs;
          if (mode === 'LINK') return LinkVfs;
          return LocalVfs;
        },
        read: async (path) => VfsManager.driver.read(path),
        write: async (path, content) => VfsManager.driver.write(path, content),
        del: async (path) => VfsManager.driver.del(path),
        list: async () => VfsManager.driver.list(),
        reveal: async (path) => {
          if (typeof VfsManager.driver.reveal === 'function') {
            return VfsManager.driver.reveal(path);
          }
        }
      };

      // [V19.0 CORE] CommitDriver (T2-3)

      const CommitDriver = {
        createCommit: async (scope = 'changes') => {
          const state = store.getState();
          const commit = {
            version: 1,
            meta: {
              runId: state.runtime.runId,
              timestamp: Date.now(),
              storageMode: state.project.storageMode,
              appVersion: '19.0'
            },
            files: [],
            deletes: []
          };

          let paths = [];
          if (scope === 'changes') {
            paths = [...state.session.syncList];
          } else {
            throw new Error(`CommitDriver: Scope '${scope}' not implemented.`);
          }

          for (const path of paths) {
            try {
              const content = await VfsManager.read(path);
              commit.files.push({
                path,
                content
              });
            } catch (e) {
              const isEnoent = e.code === 'ENOENT' || (e.message && (e.message.includes('ENOENT') || e.message.includes('not found')));
              if (isEnoent) {
                commit.deletes.push(path);
              } else {
                throw e;
              }
            }
          }
          return commit;
        }
      };

      // [V19.0 CORE] Executor (T4-1)
      // 存储层：VFS抽象/IDB持久化/Git集成/Timeline
      // - TimelineDB: IndexedDB 快照存储
      // - IDBHelper: 通用 IDB 操作
      // - GitLink: GitHub 集成
      // - VfsManager: 三模式文件系统抽象 (LOCAL/VIRTUAL/LINK)
      // - CommitDriver: 批量提交驱动

      // Timeline Snapshot Payload Store (IndexedDB)
      const TimelineDB = {
        dbName: 'Deck0_Timeline_DB',
        storeName: 'snapshots',
        db: null,

        init: () => {
          return new Promise((resolve, reject) => {
            if (TimelineDB.db) return resolve(TimelineDB.db);
            const req = indexedDB.open(TimelineDB.dbName, 1);
            req.onupgradeneeded = (e) => {
              const db = e.target.result;
              if (!db.objectStoreNames.contains(TimelineDB.storeName)) {
                db.createObjectStore(TimelineDB.storeName);
              }
            };
            req.onsuccess = (e) => {
              TimelineDB.db = e.target.result;
              resolve(TimelineDB.db);
            };
            req.onerror = (e) => reject(e.target.error);
          });
        },

        transact: (mode) => TimelineDB.db.transaction([TimelineDB.storeName], mode).objectStore(TimelineDB.storeName),

        put: async (id, payload) => {
          await TimelineDB.init();
          return new Promise((resolve, reject) => {
            const req = TimelineDB.transact('readwrite').put(payload, id);
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        },

        get: async (id) => {
          await TimelineDB.init();
          return new Promise((resolve, reject) => {
            const req = TimelineDB.transact('readonly').get(id);
            req.onsuccess = () => resolve(req.result || null);
            req.onerror = (e) => reject(e.target.error);
          });
        },

        delete: async (id) => {
          await TimelineDB.init();
          return new Promise((resolve, reject) => {
            const req = TimelineDB.transact('readwrite').delete(id);
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        }
      };

      const addTimelineSnapshot = async (type, title, files = [], snapshotFiles = null) => {
        if (!Deck0.store.getState().project.dirHandle) {
          log("Mount project first.", 'warn');
          return null;
        }

        const item = {
          id: 'snap_' + Date.now() + '_' + Math.random().toString(16).slice(2),
          type: (type || 'user').toLowerCase(), // 'user' | 'assistant'
          title: title || 'Snapshot',
          files: Array.isArray(files) ? [...new Set(files)] : [],
          timestamp: Date.now(),
          status: 'saving',
          totalFiles: 0
        };
        Deck0.store.mutate('timeline', t => { t.snapshots.push(item); });
        saveTimelineSnapshots();
        renderExecutionLog();

        try {
          const snapshotRaw = Array.isArray(snapshotFiles) ? snapshotFiles : await captureProjectState(true);

          // [V18.3 CAS] Deduplicate & Store Blobs
          const casFiles = [];
          for (const f of snapshotRaw) {
            const content = f.content || '';
            const hash = await IDBHelper.hash(content);
            // Async store blob (fire and forget or await? await for safety)
            await IDBHelper.putBlob(hash, content);

            casFiles.push({
              path: f.path,
              hash: hash,
              // content: null // Content is stripped
            });
          }

          const payload = {
            version: 2, // [V18.3] Bump payload version
            timestamp: item.timestamp,
            files: casFiles
          };

          item.totalFiles = payload.files.length;
          await TimelineDB.put(item.id, payload);
          item.status = 'ready';
          saveTimelineSnapshots();
          renderExecutionLog();
        } catch (e) {
          item.status = 'error';
          item.error = e && e.message ? e.message : String(e);
          saveTimelineSnapshots();
          renderExecutionLog();
          log(`Snapshot save failed: ${item.error}`, 'error');
        }

        return item;
      };

      const deleteTimelineSnapshot = async (snapId) => {
        const snap = (Deck0.store.getState().timeline.snapshots || []).find(s => s && s.id === snapId);
        if (!snap) return;
        const title = snap.title || 'Snapshot';
        if (!confirm(`Delete snapshot?\n\n${title}`)) return;

        Deck0.store.mutate('timeline', t => { t.snapshots = (t.snapshots || []).filter(s => s && s.id !== snapId); });
        saveTimelineSnapshots();
        renderExecutionLog();

        try {
          await TimelineDB.delete(snapId);
        } catch (e) { }
        log(`🗑️ Snapshot deleted: ${title}`, 'success');
      };

      const syncTimelineSnapshot = async (snapId) => {
        const snap = (Deck0.store.getState().timeline.snapshots || []).find(s => s && s.id === snapId);
        if (!snap) return;
        const files = Array.isArray(snap.files) ? [...new Set(snap.files)] : [];
        if (files.length === 0) return log('No changed files recorded for this snapshot.', 'warn');

        const prefix = (snap.type || '').toLowerCase() === 'assistant' ? 'feat' : 'chore';
        const title = (snap.title || 'Update Project').trim() || 'Update Project';
        const msg = `${prefix}: ${title}\n\nTimeline Snapshot: ${snap.id}\nFiles (${files.length}):\n${files.slice(0, 50).map(p => `- ${p}`).join('\n')}${files.length > 50 ? `\n- ... (+${files.length - 50} more)` : ''}`;
        await GitLink.showSyncModal(files, msg);
      };

      const previewTimelineSnapshot = async (snapId) => {
        const snap = (Deck0.store.getState().timeline.snapshots || []).find(s => s && s.id === snapId);
        if (!snap) return;
        if (!Deck0.store.getState().project.dirHandle) return log("Mount project first.", 'error');

        if (snap.status === 'saving') return log('Snapshot is still saving…', 'warn');

        let payload = null;
        try {
          payload = await TimelineDB.get(snapId);
        } catch (e) { }

        // [V18.3 CAS] Hydrate Snapshot (Resolve Hashes)
        let snapFiles = null;
        if (payload && Array.isArray(payload.files)) {
          // Check if CAS (Version 2+) or Legacy
          if (payload.version >= 2) {
            log(`📦 Hydrating CAS snapshot (${payload.files.length} refs)...`, 'info');
            snapFiles = await Promise.all(payload.files.map(async (f) => {
              let content = '';
              if (f.hash) {
                content = await IDBHelper.getBlob(f.hash);
                if (content === undefined) content = '/* [Deck0 ERROR: Blob Missing] */';
              } else {
                content = f.content || ''; // Legacy fallback inside V2? Unlikely but safe.
              }
              return { path: f.path, content: content };
            }));
          } else {
            snapFiles = payload.files; // Legacy V1 (Full Copy)
          }
        }

        if (!snapFiles) return log('Snapshot data not found (legacy snapshot or storage cleared).', 'warn');

        log(`🕰️ Loading Time Travel: "${snap.title || 'Snapshot'}"...`, 'purple');

        let currentFiles = [];
        try {
          // [V18.2] Diff against FULL disk state, ignoring selection
          currentFiles = await captureProjectState(true);
        } catch (e) {
          currentFiles = [];
        }

        // [V18.12 FIX] Overlay Editor Memory State (Unsaved Changes)
        // Solves race condition where auto-save hasn't flushed to disk yet
        if (!Deck0.store.getState().legacy.DECK0_VIEW && typeof EditorManager !== 'undefined' && Array.isArray(EditorManager.files)) {
          EditorManager.files.forEach(f => {
            if (f && f.path && typeof f.content === 'string') {
              // Only apply if looking at standard code view
              if (!f._mode || f._mode === 'code') {
                const normPath = normalizeVfsPath(f.path);
                const existing = currentFiles.find(cf => normalizeVfsPath(cf.path) === normPath);
                if (existing) existing.content = f.content;
                else currentFiles.push({ path: f.path, content: f.content });
              }
            }
          });
        }

        const norm = (p) => normalizeVfsPath(p).replace(/\\/g, '/');
        const snapMap = new Map(snapFiles.map(f => [norm(f.path), f.content || '']));
        const curMap = new Map(currentFiles.map(f => [norm(f.path), f.content || '']));
        const allPaths = [...new Set([...snapMap.keys(), ...curMap.keys()])].sort();

        const changes = [];
        const baseMap = new Map(); // current content
        const nextMap = new Map(); // snapshot content (or null for delete)
        const statusMap = new Map(); // added | modified | deleted | unchanged

        for (const path of allPaths) {
          const snapHas = snapMap.has(path);
          const curHas = curMap.has(path);
          if (snapHas && curHas) {
            const cur = curMap.get(path) || '';
            const next = snapMap.get(path) || '';
            baseMap.set(path, cur);
            nextMap.set(path, next);
            if (cur !== next) {
              statusMap.set(path, 'modified');
              changes.push({
                path,
                type: 'PATCH',
                originalContent: cur,
                newContent: next,
                isNew: false
              });
            } else {
              statusMap.set(path, 'unchanged');
            }
          } else if (snapHas && !curHas) {
            baseMap.set(path, null);
            nextMap.set(path, snapMap.get(path) || '');
            statusMap.set(path, 'added');
            changes.push({
              path,
              type: 'WRITE',
              originalContent: '',
              newContent: snapMap.get(path) || '',
              isNew: true
            });
          } else if (!snapHas && curHas) {
            baseMap.set(path, curMap.get(path) || '');
            nextMap.set(path, null);
            statusMap.set(path, 'deleted');
            changes.push({
              path,
              type: 'DELETE',
              originalContent: curMap.get(path) || '',
              newContent: '',
              isNew: false
            });
          }
        }

        if (changes.length === 0) return log('Already at this snapshot (no differences).', 'success');

        // Calculate Stats
        let nAdd = 0, nMod = 0, nDel = 0;
        changes.forEach(c => {
          if (c.type === 'WRITE') nAdd++;
          else if (c.type === 'PATCH') nMod++;
          else if (c.type === 'DELETE') nDel++;
        });

        const label = (snap.type || '').toLowerCase() === 'assistant' ? 'ASSISTANT' : 'USER';
        const viewTitle = snap.title || 'Snapshot';
        const viewDesc = `${label} · ${new Date(Number(snap.timestamp) || Date.now()).toLocaleString()}`;
        const focus = changes.find(c => c.type === 'PATCH') || changes.find(c => c.type === 'WRITE') || changes[0];

        // Enter in-editor Time Travel mode (Dev0-style): diff in editor, apply via mode bar.
        Deck0Actions.legacy.setView({
          mode: 'time_travel',
          snapshotId: snapId, // [V18.11 UI] For Timeline Highlight
          preview: true,
          readOnly: true,
          title: viewTitle,
          desc: viewDesc,
          baseMap,
          virtualMap: nextMap,
          statusMap,
          confirmText: 'Restore this version',
          cancelText: 'Exit Time Travel',
          confirmBg: THEME.success,
          onConfirm: async () => {
            log(`🕰️ Restoring snapshot (${changes.length} file changes)...`, 'warn');
            let wrote = 0,
              deleted = 0;

            for (const c of changes) {
              const p = normalizeVfsPath(c.path);
              if (!p || !isSafeVfsPath(p)) continue;
              try {
                if (c.type === 'DELETE') {
                  if (await Executor.delete(p)) {
                    deleted++;
                    try {
                      await cleanupEmptyParents(p);
                    } catch (e) { }
                  }
                } else {
                  if (await Executor.write(p, c.newContent || '')) wrote++;
                }
              } catch (e) { }
            }

            // Adopt snapshot as new baseline
            try {
              Deck0.store.mutate('project', p => {
                p.baselineSnapshot = snapFiles.map(f => ({
                  path: norm(f.path),
                  content: f.content || ''
                }));
              });
            } catch (e) { }

            // Clear AI run state (time travel is user-driven)
            Deck0Actions.session.resetPendingChanges();
            try {
              const convId = SnapshotManager.currentConversationId;
              if (convId) SnapshotManager.conversations.delete(convId);
              SnapshotManager.currentConversationId = null;
            } catch (e) { }

            Deck0Actions.legacy.clearView();
            updateEditorModeBar();
            try {
              await refreshFileTree(true);
            } catch (e) { }
            try {
              if (EditorManager && typeof EditorManager.refreshOpenFiles === 'function') await EditorManager.refreshOpenFiles();
            } catch (e) { }
            try {
              await renderChangedFiles();
            } catch (e) { }

            log(`✅ Time travel applied (wrote: ${wrote}, deleted: ${deleted})`, 'success');
          },
          onCancel: async () => {
            Deck0Actions.legacy.clearView();
            updateEditorModeBar();
            renderExecutionLog(); // [V18.14] Clear Timeline Highlight
            try {
              if (EditorManager && typeof EditorManager.refreshOpenFiles === 'function') await EditorManager.refreshOpenFiles();
              await refreshFileTree(true); // [V18.14] Restore Explorer
            } catch (e) { }
            log('Exited Time Travel.', 'info');
          }
        });

        updateEditorModeBar();
        renderExecutionLog(); // Update Timeline Highlights
        await refreshFileTree(true); // Apply diff focus
        try {
          if (focus && focus.path) await EditorManager.open(focus.path);
        } catch (e) { }
        log(`✅ Time Travel Ready: ${nMod} mod, ${nAdd} add, ${nDel} del`, 'success');
      };

      const renderExecutionLog = () => {
        if (timelineTitleEl) timelineTitleEl.textContent = `TIMELINE (${(Deck0.store.getState().timeline.snapshots || []).length})`;
        executionLogsView.replaceChildren();

        const items = Array.isArray(Deck0.store.getState().timeline.snapshots) ? [...Deck0.store.getState().timeline.snapshots].reverse() : [];
        const activeSnapId = (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode === 'time_travel') ? Deck0.store.getState().legacy.DECK0_VIEW.snapshotId : null;
        if (items.length === 0) {
          el('div', {
            text: 'No snapshots yet. Press Ctrl+S to create one.',
            style: {
              padding: '10px',
              color: '#666',
              fontSize: '11px',
              lineHeight: '1.4'
            },
            parent: executionLogsView
          });
          return;
        }

        items.forEach((snap, idx) => {
          const isLatest = idx === 0;
          const label = (snap.type || '').toLowerCase() === 'assistant' ? 'ASSISTANT' : 'USER';
          const fileCount = Array.isArray(snap.files) ? snap.files.length : 0;
          const title = snap.title || 'Snapshot';
          const status = (snap.status || '').toLowerCase();
          const statusText = status === 'saving' ? ' · SAVING' : (status === 'error' ? ' · ERROR' : '');
          const meta = `${label}${timeAgo(snap.timestamp) ? ' · ' + timeAgo(snap.timestamp) : ''}${statusText}`;

          const row = el('div', {
            cls: `deck0-timeline-item ${activeSnapId === snap.id ? 'viewing' : ''}`,
            parent: executionLogsView,
            attrs: {
              'data-snap-id': snap.id
            },
            click: async (e) => {
              if (e.target && e.target.closest && e.target.closest('.deck0-timeline-action-btn')) return;
              await previewTimelineSnapshot(snap.id);
            }
          });

          el('div', {
            cls: 'deck0-timeline-line',
            parent: row
          });
          el('div', {
            cls: `deck0-timeline-dot ${isLatest ? 'active' : ''}`,
            parent: row
          });

          const content = el('div', {
            cls: 'deck0-timeline-content',
            parent: row
          });
          el('div', {
            cls: 'deck0-timeline-title',
            text: `${title}${fileCount ? ` · ${fileCount} files changed` : ''}`,
            parent: content
          });
          const metaRow = el('div', {
            cls: 'deck0-timeline-meta-row',
            parent: content
          });
          el('div', {
            cls: 'deck0-timeline-meta-text',
            text: meta,
            parent: metaRow
          });
          const actions = el('div', {
            cls: 'deck0-timeline-actions',
            parent: metaRow
          });
          el('button', {
            cls: 'deck0-timeline-action-btn cloud',
            text: '☁',
            attrs: {
              title: 'Sync Snapshot'
            },
            parent: actions,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              await syncTimelineSnapshot(snap.id);
            }
          });
          el('button', {
            cls: 'deck0-timeline-action-btn delete',
            text: '🗑',
            attrs: {
              title: 'Delete Snapshot'
            },
            parent: actions,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              await deleteTimelineSnapshot(snap.id);
            }
          });
        });
      };

      const showSnapshotModal = (defaultTitle, onConfirm) => {
        const overlay = el('div', {
          cls: 'deck0-modal-overlay',
          parent: document.body
        });
        const win = el('div', {
          cls: 'deck0-modal-win',
          style: {
            height: 'auto',
            maxWidth: '400px'
          },
          parent: overlay
        });
        const header = el('div', {
          cls: 'deck0-modal-header',
          children: [
            el('div', { cls: 'deck0-modal-title', text: 'CREATE SNAPSHOT' }),
            el('div', { text: '✕', style: { cursor: 'pointer' }, click: () => overlay.remove() })
          ],
          parent: win
        });
        const body = el('div', {
          cls: 'deck0-modal-body',
          style: { flexDirection: 'column', padding: '20px', gap: '10px', overflow: 'visible' },
          parent: win
        });
        el('label', {
          text: 'Snapshot Title:',
          style: { fontSize: '11px', color: '#888', fontWeight: 'bold' },
          parent: body
        });
        const input = el('input', {
          cls: 'deck0-input-area',
          style: { border: '1px solid #444', height: '32px', padding: '5px', borderRadius: '4px' },
          attrs: { value: defaultTitle || '', placeholder: 'Enter snapshot title...' },
          parent: body
        });
        const footer = el('div', {
          cls: 'deck0-modal-footer',
          parent: win
        });
        el('button', {
          cls: 'deck0-btn',
          text: 'Cancel',
          style: { background: '#333', marginRight: '8px' },
          parent: footer,
          click: () => overlay.remove()
        });
        const confirmBtn = el('button', {
          cls: 'deck0-btn',
          text: 'Create Snapshot',
          style: { background: THEME.accent },
          parent: footer,
          click: () => {
            const val = input.value.trim() || defaultTitle;
            overlay.remove();
            onConfirm(val);
          }
        });
        Deck0Effects.clock.setTimeout(() => { input.focus(); input.select(); }, 50);
        input.addEventListener('keydown', (e) => {
          if (e.key === 'Enter') confirmBtn.click();
          if (e.key === 'Escape') overlay.remove();
        });
      };

      const setupTimelineHotkeys = () => {
        document.addEventListener('keydown', async (e) => {
          if (!e.isTrusted) return;
          const isSave = (e.key === 's' || e.key === 'S' || e.code === 'KeyS');
          if (!isSave) return;
          if (!e.ctrlKey && !e.metaKey) return;
          if (e.repeat) return;

          e.preventDefault();
          e.stopPropagation();

          showSnapshotModal('User Snapshot', async (customTitle) => {
            const files = [...new Set(Deck0.store.getState().session.syncList)];
            let snapshotFiles = null;
            try {
              // [V18.2] User Snapshots must be complete
              snapshotFiles = await captureProjectState(true);
            } catch (e) {
              snapshotFiles = null;
            }
            const created = await addTimelineSnapshot('user', customTitle, files, snapshotFiles);
            if (created) log(`📌 User snapshot created`, 'success');
          });
        }, true);
      };

      // ================= IDB HELPER (VIRTUAL STORAGE & CAS) =================
      const IDBHelper = {
        dbName: 'Deck0_Virtual_Disk',
        storeName: 'files',
        blobStoreName: 'blobs', // [V18.3 CAS]
        db: null,

        // [V18.3] Fast SHA-1 Hash
        hash: async (content) => {
          const msgBuffer = new TextEncoder().encode(content || '');
          const hashBuffer = await crypto.subtle.digest('SHA-1', msgBuffer);
          const hashArray = Array.from(new Uint8Array(hashBuffer));
          return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
        },

        init: () => {
          return new Promise((resolve, reject) => {
            if (IDBHelper.db) return resolve(IDBHelper.db);
            // [V18.3] Bump Version to 2 for BlobStore
            const req = indexedDB.open(IDBHelper.dbName, 2);
            req.onupgradeneeded = (e) => {
              const db = e.target.result;
              if (!db.objectStoreNames.contains(IDBHelper.storeName)) {
                db.createObjectStore(IDBHelper.storeName);
              }
              // [V18.3 CAS] Blob Store
              if (!db.objectStoreNames.contains(IDBHelper.blobStoreName)) {
                db.createObjectStore(IDBHelper.blobStoreName);
              }
            };
            req.onsuccess = (e) => {
              IDBHelper.db = e.target.result;
              resolve(IDBHelper.db);
            };
            req.onerror = (e) => reject(e.target.error);
          });
        },

        transact: (mode, store = IDBHelper.storeName) => IDBHelper.db.transaction([store], mode).objectStore(store),

        // Blob Ops
        putBlob: async (hash, content) => {
          await IDBHelper.init();
          // Check existence first to avoid redundant writes? IDB put is fast enough.
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readwrite', IDBHelper.blobStoreName).put(content, hash);
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        },

        getBlob: async (hash) => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readonly', IDBHelper.blobStoreName).get(hash);
            req.onsuccess = () => resolve(req.result);
            req.onerror = (e) => reject(e.target.error);
          });
        },

        put: async (path, content) => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readwrite').put(content, path);
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        },

        get: async (path) => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readonly').get(path);
            req.onsuccess = () => resolve(req.result);
            req.onerror = (e) => reject(e.target.error);
          });
        },

        delete: async (path) => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readwrite').delete(path);
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        },

        getAllKeys: async () => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readonly').getAllKeys();
            req.onsuccess = () => resolve(req.result);
            req.onerror = (e) => reject(e.target.error);
          });
        },

        clear: async () => {
          await IDBHelper.init();
          return new Promise((resolve, reject) => {
            const req = IDBHelper.transact('readwrite').clear();
            req.onsuccess = () => resolve();
            req.onerror = (e) => reject(e.target.error);
          });
        }
      };

      // [V17.3] Branch Manager Logic Removed (Simplified UI)

      // ============================================================
      // 🚀 Deck0 GITHUB LINK ENGINE (NATIVE FETCH - NO OCTOKIT)
      // ============================================================
      const GitLink = {
        // Core Fetch Wrapper for GitHub API (Bypasses CSP script-src)
        request: async (method, endpoint, body = null) => {
          // [TP-P3-4] Explicit dependencies
          const { storageKeys } = window.Deck0.constants;

          const token = Deck0Effects.persist.get(storageKeys.GH_TOKEN);
          if (!token) throw new Error('No Token');

          let url = `https://api.github.com${endpoint}`;
          // [FIX] Add Timestamp to prevent caching (Fixes Branch List not updating after delete)
          if (method === 'GET') {
            const sep = url.includes('?') ? '&' : '?';
            url = `${url}${sep}_t=${Date.now()}`;
          }

          const headers = {
            'Authorization': `token ${token}`,
            'Accept': 'application/vnd.github.v3+json',
            'Content-Type': 'application/json'
          };

          const config = {
            method,
            headers
          };
          if (body) config.body = JSON.stringify(body);

          const res = await fetch(url, config);
          if (res.status === 204) return {};
          const data = await res.json();

          if (!res.ok) throw new Error(data.message || res.statusText);
          return data;
        },

        showConfigModal: () => {
          // [TP-P3-4] Explicit dependencies
          const { el } = window.Deck0.ui;
          const { theme, storageKeys } = window.Deck0.constants;

          const storedToken = Deck0Effects.persist.get(storageKeys.GH_TOKEN) || '';
          const storedRepo = Deck0Effects.persist.get(storageKeys.GH_REPO) || '';

          const overlay = el('div', {
            cls: 'deck0-modal-overlay',
            parent: document.body
          });
          const win = el('div', {
            cls: 'deck0-modal-win',
            style: {
              height: 'auto',
              maxHeight: '400px',
              maxWidth: '500px'
            },
            parent: overlay
          });

          el('div', {
            cls: 'deck0-modal-header',
            children: [
              el('div', {
                cls: 'deck0-modal-title',
                text: 'GITHUB SYNC CONFIG (NATIVE)'
              }),
              el('div', {
                text: '✕',
                style: {
                  cursor: 'pointer'
                },
                click: () => overlay.remove()
              })
            ],
            parent: win
          });

          const body = el('div', {
            cls: 'deck0-modal-body',
            style: {
              flexDirection: 'column',
              padding: '20px',
              gap: '15px',
              overflow: 'visible'
            },
            parent: win
          });

          el('label', {
            text: 'Repository (owner/name):',
            style: {
              color: '#888',
              fontSize: '12px'
            },
            parent: body
          });
          const repoInput = el('input', {
            cls: 'deck0-input-area',
            style: {
              border: '1px solid #444',
              height: '32px',
              padding: '5px',
              borderRadius: '4px'
            },
            attrs: {
              value: storedRepo,
              placeholder: 'e.g. facebook/react'
            },
            parent: body
          });

          el('label', {
            text: 'Personal Access Token (Repo Scope):',
            style: {
              color: '#888',
              fontSize: '12px'
            },
            parent: body
          });
          const tokenInput = el('input', {
            cls: 'deck0-input-area',
            style: {
              border: '1px solid #444',
              height: '32px',
              padding: '5px',
              borderRadius: '4px'
            },
            attrs: {
              type: 'password',
              value: storedToken,
              placeholder: 'ghp_...'
            },
            parent: body
          });

          const footer = el('div', {
            cls: 'deck0-modal-footer',
            parent: win
          });
          el('button', {
            cls: 'deck0-btn',
            text: 'Save & Connect',
            style: {
              background: theme.success
            },
            parent: footer,
            click: async () => {
              let r = repoInput.value.trim();
              // Auto-sanitize input
              r = r.replace(/^https?:\/\/github\.com\//, '').replace(/\.git$/, '');

              const t = tokenInput.value.trim();

              if (!r.includes('/') || r.split('/').length !== 2) return alert('Invalid Repo Format. Use "owner/repo" (e.g. facebook/react)');
              if (t.length < 10) return alert('Token too short. Must be a valid GH PAT.');

              Deck0Effects.persist.set(storageKeys.GH_REPO, r);
              Deck0Effects.persist.set(storageKeys.GH_TOKEN, t);

              overlay.remove();
              await GitLink.init();
            }
          });
        },

        init: async () => {
          // [TP-P3-4] Explicit dependencies
          const { log } = window.Deck0.ui;
          const { storageKeys } = window.Deck0.constants;

          const token = Deck0Effects.persist.get(storageKeys.GH_TOKEN);
          if (token) {
            try {
              const user = await GitLink.request('GET', '/user');
              log(`✅ Git Connected: ${user.login}`, 'success');
              return true;
            } catch (e) {
              log(`❌ Git Auth Failed: ${e.message}`, 'error');
              GitLink.showConfigModal();
              return false;
            }
          } else {
            GitLink.showConfigModal();
            return false;
          }
        },

        getBranches: async () => {
          // [TP-P3-4] Explicit dependencies
          const { storageKeys } = window.Deck0.constants;

          if (!await GitLink.init()) return [];
          const repoStr = Deck0Effects.persist.get(storageKeys.GH_REPO);
          const [owner, repo] = repoStr.split('/');
          const branches = await GitLink.request('GET', `/repos/${owner}/${repo}/branches`);
          return branches.map(b => b.name);
        },

        deleteBranch: async (branchName) => {
          // [TP-P3-4] Explicit dependencies
          const { log } = window.Deck0.ui;
          const { storageKeys } = window.Deck0.constants;

          if (!await GitLink.init()) return;
          const repoStr = Deck0Effects.persist.get(storageKeys.GH_REPO);
          const [owner, repo] = repoStr.split('/');
          try {
            await GitLink.request('DELETE', `/repos/${owner}/${repo}/git/refs/heads/${branchName}`);
            log(`🗑️ Deleted branch: ${branchName}`, 'success');
            return true;
          } catch (e) {
            log(`❌ Delete Failed: ${e.message}`, 'error');
            return false;
          }
        },

        // [V15.9 CORE] Pull Engine
        pullBranch: async (branchName) => {
          // [TP-P3-4] Explicit dependencies
          const { log } = window.Deck0.ui;
          const { theme, storageKeys } = window.Deck0.constants;
          const executor = window.Deck0.executor;
          const { refresh } = window.Deck0.ui.tree;
          const { set } = window.Deck0.ui.status;

          if (!await GitLink.init()) return;
          const repoStr = Deck0Effects.persist.get(storageKeys.GH_REPO);
          const [owner, repo] = repoStr.split('/');

          log(`⬇ Pulling branch "${branchName}"...`, 'purple');

          try {
            // 1. Get Branch SHA
            const ref = await GitLink.request('GET', `/repos/${owner}/${repo}/git/refs/heads/${branchName}`);
            const commitSha = ref.object.sha;

            // 2. Get Tree (Recursive)
            const commitObj = await GitLink.request('GET', `/repos/${owner}/${repo}/git/commits/${commitSha}`);
            const treeSha = commitObj.tree.sha;
            const treeData = await GitLink.request('GET', `/repos/${owner}/${repo}/git/trees/${treeSha}?recursive=1`);

            if (treeData.truncated) log("⚠️ Warning: Repo too large, some files may be missing.", 'warn');

            const files = treeData.tree.filter(t => t.type === 'blob');
            log(`📦 Found ${files.length} files. Downloading...`, 'info');

            let successCount = 0;

            // 3. Fetch and Write Blobs
            // We process in chunks to avoid overwhelming the browser/network
            for (const file of files) {
              try {
                const blobData = await GitLink.request('GET', `/repos/${owner}/${repo}/git/blobs/${file.sha}`);

                // Decode Base64 (UTF-8 Safe)
                const content = decodeURIComponent(escape(window.atob(blobData.content.replace(/\s/g, ''))));

                await executor.write(file.path, content);
                successCount++;

                // Simple progress indicator in logs every 5 files
                if (successCount % 5 === 0) set(`PULL ${Math.round((successCount / files.length) * 100)}%`, theme.accent);

              } catch (err) {
                log(`❌ Failed to pull ${file.path}: ${err.message}`, 'error');
              }
            }

            log(`✅ Pull Complete. ${successCount}/${files.length} files updated.`, 'success');
            await refresh(false);
            set('READY', theme.success);

          } catch (e) {
            log(`❌ Pull Error: ${e.message}`, 'error');
            set('ERROR', theme.danger);
          }
        },

        showSyncModal: async (fileList, commitMsg) => {
          // [TP-P3-4] Explicit dependencies
          const { el, log } = window.Deck0.ui;
          const { theme, storageKeys } = window.Deck0.constants;

          if (!await GitLink.init()) return;

          // Fetch branches first
          log("Fetching branches...", 'info');
          let branches = [];
          try {
            branches = await GitLink.getBranches();
          } catch (e) {
            log("Could not fetch branches", 'warn');
          }

          const overlay = el('div', {
            cls: 'deck0-modal-overlay',
            parent: document.body
          });
          const win = el('div', {
            cls: 'deck0-modal-win',
            style: {
              height: 'auto',
              maxHeight: '650px',
              maxWidth: '500px'
            },
            parent: overlay
          });

          el('div', {
            cls: 'deck0-modal-header',
            children: [
              el('div', {
                cls: 'deck0-modal-title',
                text: 'SYNC SETTINGS'
              }),
              el('div', {
                text: '✕',
                style: {
                  cursor: 'pointer'
                },
                click: () => overlay.remove()
              })
            ],
            parent: win
          });

          const body = el('div', {
            cls: 'deck0-modal-body',
            style: {
              flexDirection: 'column',
              padding: '20px',
              gap: '15px',
              overflowY: 'auto'
            },
            parent: win
          });

          // Branch Selection
          el('label', {
            text: 'Target Branch:',
            style: {
              color: theme.accent,
              fontSize: '11px',
              fontWeight: 'bold',
              textTransform: 'uppercase'
            },
            parent: body
          });

          const select = el('select', {
            cls: 'deck0-input-area',
            style: {
              border: '1px solid #444',
              height: '32px',
              padding: '5px',
              borderRadius: '4px',
              background: '#222',
              marginBottom: '4px'
            },
            parent: body
          });
          el('option', {
            text: '✨ Create New Branch',
            attrs: {
              value: ''
            },
            parent: select
          });
          branches.forEach(b => {
            el('option', {
              text: `↳ ${b}`,
              attrs: {
                value: b
              },
              parent: select
            });
          });

          const input = el('input', {
            cls: 'deck0-input-area',
            style: {
              border: '1px solid #444',
              height: '32px',
              padding: '5px',
              borderRadius: '4px'
            },
            attrs: {
              value: `deck0-sync-${Date.now()}`
            },
            parent: body
          });

          select.addEventListener('change', () => {
            if (select.value) {
              input.style.display = 'none';
            } else {
              input.style.display = 'block';
              input.focus();
            }
          });

          // Commit Message Editor
          el('label', {
            text: 'Commit Message:',
            style: {
              color: theme.accent,
              fontSize: '11px',
              fontWeight: 'bold',
              textTransform: 'uppercase',
              marginTop: '10px'
            },
            parent: body
          });
          const msgEditor = el('textarea', {
            cls: 'deck0-input-area',
            style: {
              border: '1px solid #444',
              height: '120px',
              padding: '10px',
              borderRadius: '4px',
              background: '#1a1a1a',
              resize: 'vertical'
            },
            parent: body
          });
          msgEditor.value = commitMsg || '';

          const footer = el('div', {
            cls: 'deck0-modal-footer',
            parent: win
          });
          el('button', {
            cls: 'deck0-btn',
            text: 'Push to GitHub',
            style: {
              background: theme.accent
            },
            parent: footer,
            click: async () => {
              const targetBranch = select.value || input.value.trim();
              const finalMsg = msgEditor.value.trim();

              if (!targetBranch) return alert('Branch name required');
              if (!finalMsg) return alert('Commit message required');

              overlay.remove();
              GitLink.syncFiles(fileList, finalMsg, targetBranch);
            }
          });
        },

        syncFiles: async (fileList, commitMsg, targetBranch = null) => {
          // [TP-P3-4] Explicit dependencies
          const { log } = window.Deck0.ui;
          const { theme, storageKeys } = window.Deck0.constants;
          const vfs = window.Deck0.vfs;
          const { set } = window.Deck0.ui.status;

          if (!await GitLink.init()) return;

          let repoStr = Deck0Effects.persist.get(storageKeys.GH_REPO) || '';
          repoStr = repoStr.replace(/^https?:\/\/github\.com\//, '').replace(/\.git$/, '').trim();

          const parts = repoStr.split('/');
          if (parts.length !== 2 || !parts[0] || !parts[1]) {
            return log(`❌ Invalid Repo Format: "${repoStr}". Use "owner/repo"`, 'error');
          }
          const [owner, repo] = parts;

          const branchName = targetBranch || `deck0-sync-${Date.now()}`;

          log(`🚀 Syncing ${fileList.length} files to ${owner}/${repo} on "${branchName}"...`, 'purple');

          try {
            // 1. Get Base SHA (Main or Master) OR Target Branch Head
            let baseSha = null;
            let isNewBranch = true;

            // [FIX] Avoid 404 Console Errors by listing refs instead of direct probing
            const allRefs = await GitLink.request('GET', `/repos/${owner}/${repo}/git/refs/heads?per_page=100`);

            const targetRef = allRefs.find(r => r.ref === `refs/heads/${branchName}`);

            if (targetRef) {
              baseSha = targetRef.object.sha;
              isNewBranch = false;
              log(`ℹ️ Updating existing branch: ${branchName}`, 'info');
            } else {
              // Branch doesn't exist, find main or master in the list (Silent Check)
              const mainRef = allRefs.find(r => r.ref === 'refs/heads/main') || allRefs.find(r => r.ref === 'refs/heads/master');

              if (mainRef) {
                baseSha = mainRef.object.sha;
              } else {
                // Fallback: If main/master not in first 100 refs, try specific fetch (last resort)
                try {
                  const manualMain = await GitLink.request('GET', `/repos/${owner}/${repo}/git/ref/heads/main`);
                  baseSha = manualMain.object.sha;
                } catch (e) {
                  throw new Error('Could not find base branch (main/master).');
                }
              }
            }

            // 2. Create Branch Ref if needed
            if (isNewBranch) {
              await GitLink.request('POST', `/repos/${owner}/${repo}/git/refs`, {
                ref: `refs/heads/${branchName}`,
                sha: baseSha
              });
              log(`🌿 Created branch: ${branchName}`, 'info');
            }

            // 3. Create Blobs
            const treeItems = [];
            for (const item of fileList) {
              const path = typeof item === 'string' ? item : (item && item.path);
              if (!path) continue;

              let content = null;
              let isDeleted = false;
              try {
                content = await vfs.read(path);
              } catch (e) {
                // If read fails, assume deleted
                isDeleted = true;
              }

              if (isDeleted) {
                // DELETE: Mark with sha: null
                treeItems.push({
                  path: path,
                  mode: '100644',
                  type: 'blob',
                  sha: null
                });
                log(`🗑️ Mark for delete: ${path}`, 'info');
              } else {
                // WRITE/UPDATE: Create Blob
                const blob = await GitLink.request('POST', `/repos/${owner}/${repo}/git/blobs`, {
                  content: content,
                  encoding: 'utf-8'
                });

                treeItems.push({
                  path: path,
                  mode: '100644',
                  type: 'blob',
                  sha: blob.sha
                });
              }
            }

            if (treeItems.length === 0) return log('⚠️ No file changes detected to push.', 'warn');

            // 4. Create Tree
            // We need the TREE SHA of the base commit, not just the commit SHA
            const baseCommitObj = await GitLink.request('GET', `/repos/${owner}/${repo}/git/commits/${baseSha}`);
            const treeRes = await GitLink.request('POST', `/repos/${owner}/${repo}/git/trees`, {
              base_tree: baseCommitObj.tree.sha,
              tree: treeItems
            });

            // 5. Create Commit
            const commitRes = await GitLink.request('POST', `/repos/${owner}/${repo}/git/commits`, {
              message: commitMsg,
              tree: treeRes.sha,
              parents: [baseSha]
            });

            // 6. Update Ref (Push)
            await GitLink.request('PATCH', `/repos/${owner}/${repo}/git/refs/heads/${branchName}`, {
              sha: commitRes.sha
            });

            log(`✅ Sync Complete: ${commitRes.html_url}`, 'success');
            set('SYNCED', theme.success);

            // 7. Create PR (Only if new branch or explicitly requested? Keep logic simple: Try PR, ignore if exists/same)
            if (isNewBranch) {
              try {
                // Extract title from first line of commit message for PR title
                const lines = commitMsg.split('\n');
                const prTitle = lines[0].length > 70 ? lines[0].substring(0, 67) + '...' : lines[0];

                const pr = await GitLink.request('POST', `/repos/${owner}/${repo}/pulls`, {
                  title: prTitle || 'Deck0 Sync Update',
                  head: branchName,
                  base: 'main', // TODO: Detect default branch dynamically? usually main or master.
                  body: `${commitMsg}\n\n---\n*Automated PR generated by Deck0. Includes ${fileList.length} file(s).*`
                });
                log(`🔗 PR Created: #${pr.number}`, 'success');
                if (confirm(`Sync Success! Open Pull Request #${pr.number}?`)) {
                  window.open(pr.html_url, '_blank');
                }
              } catch (e) {
                // PR might fail if no diff or already exists
                log(`(PR not created: ${e.message})`, 'warn');
              }
            }

          } catch (e) {
            log(`❌ Sync Error: ${e.message}`, 'error');
            console.error(e);
          }
        }
      };

      // =========================================================
      // ⚡ V15.0 DIFF ENGINE & MODAL & FLYOUT
      // =========================================================

      // [V15.2.0] TitanHighlighter - The "World-Class" Embedded Lexer
      // Features: Priority-based Tokenization, No-Bleeding, VSCode Dark+ Palette
      const NanoHighlighter = TitanHighlighter; // Alias for backward compat
      // Alias for compatibility with previous calls
      const MicroHighlighter = NanoHighlighter;
      // [V19.0 LOGIC] Expose for Architecture
      window.TitanHighlighter = TitanHighlighter;

      const startHideFlyoutTimer = () => {
        if (hideFlyoutTimer) Deck0Effects.clock.clearTimeout(hideFlyoutTimer);
        hideFlyoutTimer = Deck0Effects.clock.setTimeout(() => {
          planDetailsFlyout.classList.remove('visible');
        }, 200);
      };

      const planDetailsFlyout = el('div', {
        id: 'deck0-plan-details-flyout',
        parent: grid,
        mouseenter: () => {
          if (hideFlyoutTimer) Deck0Effects.clock.clearTimeout(hideFlyoutTimer);
        },
        mouseleave: startHideFlyoutTimer
      });

      const getPlanDetails = (planId) => {
        const plan = store.getState().protocol.plans.find(p => p.id === planId);
        if (!plan) return null;

        const actions = plan.ops || [];

        const fileMap = new Map();
        actions.forEach(act => {
          if (!fileMap.has(act.path)) {
            fileMap.set(act.path, {
              path: act.path,
              type: act.type,
              hunks: 0
            });
          }
          const fileData = fileMap.get(act.path);

          if (act.type.toLowerCase() === 'patch') {
            // In new parser, check action.type or presence of searchBlock
            // For simplicity, increment hunk count if it's a patch
            fileData.hunks += 1;
          }
        });

        return {
          ...plan,
          files: Array.from(fileMap.values())
        };
      };

      const showPlanDetails = (planId, cardElement) => {
        if (hideFlyoutTimer) Deck0Effects.clock.clearTimeout(hideFlyoutTimer);
        const details = getPlanDetails(planId);
        if (!details) return;

        const rect = cardElement.getBoundingClientRect();
        const gridRect = grid.getBoundingClientRect();

        planDetailsFlyout.style.top = `${rect.top - gridRect.top}px`;
        planDetailsFlyout.style.right = `${gridRect.width - rect.left + 10}px`;

        planDetailsFlyout.replaceChildren();

        const prefix = details.id.split('-')[0];
        const tagStyle = TAG_COLORS[prefix] || {
          bg: 'rgba(255,255,255,0.1)',
          text: '#fff'
        };

        el('div', {
          cls: 'flyout-title',
          parent: planDetailsFlyout,
          children: [
            el('span', {
              cls: 'deck0-tag',
              text: details.id,
              style: {
                background: tagStyle.bg,
                color: tagStyle.text
              }
            }),
            el('span', {
              text: details.title
            })
          ]
        });

        el('div', {
          cls: 'flyout-desc',
          text: details.desc,
          parent: planDetailsFlyout
        });

        if (details.files.length > 0) {
          el('div', {
            cls: 'flyout-section-title',
            text: `Files Affected (${details.files.length})`,
            parent: planDetailsFlyout
          });
          const fileList = el('div', {
            cls: 'flyout-file-list',
            parent: planDetailsFlyout
          });
          details.files.forEach(file => {
            let statusIcon = '📝';
            if (file.type === 'write') statusIcon = '✨';
            if (file.type === 'delete') statusIcon = '🔴';

            let detailText = ` ${file.type.toUpperCase()}`;
            if (file.type === 'patch' && file.hunks > 0) {
              detailText += ` (${file.hunks} ${file.hunks > 1 ? 'hunks' : 'hunk'})`;
            }

            el('div', {
              cls: 'flyout-file-item',
              text: `${statusIcon} ${file.path}`,
              parent: fileList,
              children: [
                el('span', {
                  style: {
                    color: THEME.textDim,
                    marginLeft: '8px'
                  },
                  text: detailText
                })
              ]
            });
          });
        }

        planDetailsFlyout.classList.add('visible');
      };

      window.DiffEngine = DiffEngine;

      // Accepts either flat fileChanges (Single Plan) or grouped planGroups (Batch)
      const showDiffModal = (data, opts = {}) => {
        return new Promise((resolve) => {
          let hunkElements = [];
          let currentHunkIndex = -1;
          let planGroups = [];

          // Normalize Input
          if (data.length > 0 && data[0].plan) {
            planGroups = data; // Already grouped
          } else if (data.length > 0) {
            // Single plan flat list -> wrap in dummy group for rendering
            planGroups = [{
              plan: {
                id: 'CURRENT',
                title: 'Single Plan Execution',
                desc: 'Review changes before applying.'
              },
              changes: data
            }];
          }

          // [V17.1] Aggregate Changes by File Path (Merge Hunks)
          planGroups.forEach(group => {
            const fileMap = new Map();
            group.changes.forEach(change => {
              if (!fileMap.has(change.path)) {
                fileMap.set(change.path, {
                  ...change
                });
              } else {
                const existing = fileMap.get(change.path);
                existing.newContent = change.newContent;
                // Logic: If it becomes deleted, mark delete. If it was new, keep new. Else update to current op.
                if (change.type === 'DELETE') existing.type = 'DELETE';
                else if (existing.type === 'WRITE') {
                  /* Keep WRITE */
                } else existing.type = change.type;
              }
            });
            group.changes = Array.from(fileMap.values());
          });

          const totalFiles = planGroups.reduce((acc, g) => acc + g.changes.length, 0);
          const titleText = (opts && typeof opts.title === 'string' && opts.title.trim()) ?
            opts.title.trim() :
            `PRE-FLIGHT CHECK (${planGroups.length} Plans / ${totalFiles} Files)`;
          const cancelText = (opts && typeof opts.cancelText === 'string' && opts.cancelText.trim()) ?
            opts.cancelText.trim() :
            'Cancel';
          const confirmText = (opts && typeof opts.confirmText === 'string' && opts.confirmText.trim()) ?
            opts.confirmText.trim() :
            'Confirm ALL Changes';
          const confirmBg = (opts && opts.confirmBg) ? opts.confirmBg : THEME.success;

          // Prefer in-editor diff view (no blocking preflight modal)
          try {
            if (typeof EditorManager !== 'undefined' && EditorManager && typeof EditorManager.openDiff === 'function') {
              const editable = (opts && typeof opts.editable === 'boolean') ? opts.editable : true;
              const readOnly = !!(opts && opts.readOnly);
              EditorManager.openDiff({
                title: titleText,
                planGroups,
                opts: {
                  ...(opts || {}),
                  title: titleText,
                  cancelText,
                  confirmText,
                  confirmBg,
                  editable,
                  readOnly
                },
                resolve
              });
              return;
            }
          } catch (e) { }

          const overlay = el('div', {
            cls: 'deck0-modal-overlay',
            parent: document.body
          });
          const win = el('div', {
            cls: 'deck0-modal-win',
            parent: overlay
          });

          const header = el('div', {
            cls: 'deck0-modal-header',
            parent: win
          });
          // [V15.1.4 UX FIX]: Swapped order to (Plans / Files)
          el('div', {
            cls: 'deck0-modal-title',
            text: titleText,
            parent: header
          });
          el('div', {
            text: '✕',
            style: {
              cursor: 'pointer',
              color: '#aaa'
            },
            parent: header,
            click: () => {
              overlay.remove();
              resolve(false);
            }
          });

          const body = el('div', {
            cls: 'deck0-modal-body',
            parent: win
          });
          const sidebar = el('div', {
            cls: 'deck0-diff-sidebar',
            parent: body
          });
          const diffArea = el('div', {
            cls: 'deck0-diff-content',
            parent: body
          });

          // Render Sidebar with Hierarchy
          planGroups.forEach((group, gIdx) => {
            const plan = group.plan;
            const prefix = plan.id.split('-')[0];
            const tagStyle = TAG_COLORS[prefix] || {
              bg: '#444',
              text: '#ccc'
            };

            const header = el('div', {
              cls: 'deck0-diff-plan-header',
              parent: sidebar
            });
            const titleRow = el('div', {
              cls: 'deck0-diff-plan-title-row',
              parent: header
            });
            el('div', {
              cls: 'deck0-diff-plan-id',
              text: plan.id,
              style: {
                background: tagStyle.bg,
                color: tagStyle.text
              },
              parent: titleRow
            });

            // [V15.1.4 UI FIX]: Apply Colored Titles and Text Truncation
            const titleEl = el('div', {
              cls: 'deck0-diff-plan-text',
              parent: titleRow
            });
            const titleMatch = plan.title.match(/^([a-z]+)(\(.*\))?:(.*)$/i);
            if (titleMatch) {
              const type = titleMatch[1].toLowerCase();
              const rest = titleMatch[3].trim();
              const typeColor = {
                feat: THEME.success,
                fix: THEME.danger,
                refactor: THEME.accent,
                chore: THEME.grey
              }[type] || THEME.textMain;
              el('span', {
                text: `${type}:`,
                style: {
                  color: typeColor,
                  fontWeight: '700',
                  marginRight: '4px'
                },
                parent: titleEl
              });
              titleEl.appendChild(document.createTextNode(' ' + rest));
            } else {
              titleEl.textContent = plan.title;
            }

            el('div', {
              cls: 'deck0-diff-plan-desc',
              text: plan.desc,
              parent: header
            });

            group.changes.forEach((fc, fIdx) => {
              const status = fc.type === 'DELETE' ? '🔴 ' : (fc.isNew ? '✨ ' : '📝 ');
              const item = el('div', {
                cls: `deck0-diff-file-item deck0-nested ${gIdx === 0 && fIdx === 0 ? 'active' : ''}`,
                parent: sidebar
              });

              // [V15.1.4 UI FIX]: Split icon and path to handle overflow correctly
              el('span', {
                text: status,
                style: {
                  flexShrink: 0
                },
                parent: item
              });
              el('span', {
                cls: 'deck0-file-path-text',
                text: fc.path,
                parent: item
              });

              item.onclick = () => {
                Array.from(sidebar.querySelectorAll(DECK0_SELECTORS.UI_DIFF_FILE_ITEM)).forEach(c => c.classList.remove('active'));
                item.classList.add('active');
                renderDiffView(fc);
                navigate(0); // Auto-scroll to the first hunk
              };
            });
          });

          function navigate(newIndex) {
            if (hunkElements.length === 0 || newIndex < 0 || newIndex >= hunkElements.length) {
              return;
            }
            if (currentHunkIndex !== -1 && hunkElements[currentHunkIndex]) {
              hunkElements[currentHunkIndex].classList.remove('active-hunk');
            }

            currentHunkIndex = newIndex;
            const targetElement = hunkElements[currentHunkIndex];

            targetElement.classList.add('active-hunk');
            targetElement.scrollIntoView({
              behavior: 'smooth',
              block: 'center'
            });
          }

          const renderDiffView = (fc) => {
            hunkElements = [];
            currentHunkIndex = -1;
            diffArea.replaceChildren();

            if (fc.type === 'DELETE') {
              el('div', {
                text: 'File will be DELETED.',
                style: {
                  color: THEME.danger,
                  padding: '20px'
                },
                parent: diffArea
              });
              return;
            }

            const diffs = DiffEngine.compute(fc.originalContent, fc.newContent);
            const diffViewContainer = el('div', {
              cls: 'deck0-diff-view',
              parent: diffArea
            });

            let oldLine = 1;
            let newLine = 1;
            let hunkCounter = 0;

            diffs.forEach((d) => {
              let oldNumText = '',
                newNumText = '',
                opText = '';
              let isHunk = false;
              const lineClass = `deck0-diff-${d.type}`;

              if (d.type === 'eq') {
                oldNumText = oldLine++;
                newNumText = newLine++;
              } else {
                isHunk = true;
                if (d.type === 'add') {
                  newNumText = newLine++;
                  opText = '+';
                } else if (d.type === 'del') {
                  oldNumText = oldLine++;
                  opText = '-';
                }
              }

              const lineEl = el('div', {
                cls: `deck0-diff-line ${lineClass} ${isHunk ? 'deck0-line-is-hunk' : ''}`,
                parent: diffViewContainer
              });

              lineEl.appendChild(el('div', {
                cls: 'deck0-diff-nums-container',
                children: [
                  el('div', {
                    cls: 'deck0-diff-num-old',
                    text: oldNumText
                  }),
                  el('div', {
                    cls: 'deck0-diff-num-new',
                    text: newNumText
                  })
                ]
              }));

              const opContainer = el('div', {
                cls: 'deck0-diff-op-container',
                parent: lineEl
              });
              el('span', {
                cls: 'deck0-op-symbol',
                text: opText,
                parent: opContainer
              });

              if (isHunk) {
                const currentIndex = hunkCounter;
                el('div', {
                  cls: 'deck0-hunk-nav',
                  parent: opContainer,
                  children: [
                    el('button', {
                      cls: 'deck0-nav-btn',
                      text: '▲',
                      click: (e) => {
                        e.stopPropagation();
                        navigate(currentIndex - 1);
                      }
                    }),
                    el('button', {
                      cls: 'deck0-nav-btn',
                      text: '▼',
                      click: (e) => {
                        e.stopPropagation();
                        navigate(currentIndex + 1);
                      }
                    })
                  ]
                });
                hunkElements.push(lineEl);
                hunkCounter++;
              }

              // [V15.2.0] Titan Highlighting Integration
              const codeCell = el('div', {
                cls: 'deck0-diff-code'
              });
              const ext = fc.path ? fc.path.split('.').pop() : 'txt';
              const tokens = TitanHighlighter.tokenize(d.content || ' ', ext);

              // DOM-based rendering to avoid TrustedHTML errors
              tokens.forEach(t => {
                el('span', {
                  text: t.part,
                  style: t.color ? {
                    color: t.color
                  } : {},
                  parent: codeCell
                });
              });

              lineEl.appendChild(codeCell);
            });
          };

          // Init first view
          if (planGroups.length > 0 && planGroups[0].changes.length > 0) {
            renderDiffView(planGroups[0].changes[0]);
            Deck0Effects.clock.setTimeout(() => navigate(0), 10); // [V15.95] Auto-jump to first hunk
          } else {
            el('div', {
              text: 'No changes detected.',
              style: {
                padding: '20px',
                color: '#777'
              },
              parent: diffArea
            });
          }

          const footer = el('div', {
            cls: 'deck0-modal-footer',
            parent: win
          });
          el('button', {
            cls: 'deck0-btn',
            text: cancelText,
            style: {
              background: '#333'
            },
            parent: footer,
            click: () => {
              overlay.remove();
              resolve(false);
            }
          });
          el('button', {
            cls: 'deck0-btn',
            text: confirmText,
            style: {
              background: confirmBg
            },
            parent: footer,
            click: () => {
              overlay.remove();
              resolve(true);
            }
          });
        });
      };

      // ================= SNAPSHOT MANAGER (V18 - Auto Snapshot & Undo) =================
      // [TP-P4-2 Lite] Moved BEFORE Executor to satisfy dependency order
      const SnapshotManager = {
        // Store snapshots by conversation: { conversationId: { files: Map<path, content>, timestamp } }
        conversations: new Map(),
        currentConversationId: null,

        // Start new conversation (Auto-generate ID)
        startConversation() {
          this.currentConversationId = 'conv_' + Date.now();
          this.conversations.set(this.currentConversationId, {
            files: new Map(),
            operations: [], // 记录操作顺序，支持单个撤销
            timestamp: Date.now()
          });
          log(`📸 Snapshot session started: ${this.currentConversationId}`, 'purple');
          return this.currentConversationId;
        },

        // Snapshot single file (called before execution)
        async snapshotFile(path) {
          if (!this.currentConversationId) this.startConversation();
          const conv = this.conversations.get(this.currentConversationId);

          // Skip if already snapshotted
          if (conv.files.has(path)) return;

          try {
            let content;
            content = await VfsManager.read(path);
            conv.files.set(path, {
              content: content,
              existed: true
            });
            log(`📸 Snapshotted: ${path}`, 'info');
          } catch (e) {
            // File does not exist, mark as newly created
            conv.files.set(path, {
              content: null,
              existed: false
            });
            log(`📸 Marked as new: ${path}`, 'info');
          }
        },

        // Record an operation (for individual undo)
        recordOperation(opId, files) {
          if (!this.currentConversationId) return;
          const conv = this.conversations.get(this.currentConversationId);
          conv.operations.push({
            id: opId,
            files: [...files],
            timestamp: Date.now()
          });
        },

        // Helper: Restore state
        async _restore(path, snap) {
          // [TP-P3-5] Use Executor.restore* (No side effects, no snapshots)
          const executor = window.Deck0.executor;
          if (!executor || !executor.restoreWrite) {
            console.error('Executor.restoreWrite not found');
            return;
          }

          if (snap.existed) {
            await executor.restoreWrite(path, snap.content);
          } else {
            await executor.restoreDelete(path);
          }
        },

        // Undo single operation
        async undoOperation(opId) {
          if (!this.currentConversationId) return false;
          const conv = this.conversations.get(this.currentConversationId);
          const op = conv.operations.find(o => o.id === opId);
          if (!op) return false;

          let restored = 0,
            deleted = 0;

          // 1. IO Restoration
          for (const path of op.files) {
            const snap = conv.files.get(path);
            if (!snap) continue;

            await this._restore(path, snap);
            if (snap.existed) restored++; else deleted++;
          }

          // 2. Ledger Update (SyncList / CreatedFiles)
          // We must remove these files from the session "changed" list so they don't appear in UI
          if (window.Deck0 && window.Deck0.actions) {
            op.files.forEach(path => {
              window.Deck0.actions.session.removeSyncPath(path);
              window.Deck0.actions.session.removeCreatedFile(path);
            });
          }

          log(`↩️ Undo ${opId}: restored ${restored}, deleted ${deleted}`, 'success');

          // 3. Explicit UI Refresh
          if (window.Deck0 && window.Deck0.ui) {
            if (window.Deck0.ui.tree && window.Deck0.ui.tree.refresh) await window.Deck0.ui.tree.refresh();
            if (window.Deck0.ui.files && window.Deck0.ui.files.render) await window.Deck0.ui.files.render();
            if (window.Deck0.ui.editor && window.Deck0.ui.editor.refreshOpenFiles) await window.Deck0.ui.editor.refreshOpenFiles();
          }

          return true;
        },

        // Undo all changes for the entire conversation
        async undoConversation(convId = null) {
          const targetId = convId || this.currentConversationId;
          if (!targetId) return false;
          const conv = this.conversations.get(targetId);
          if (!conv) return false;

          let restored = 0,
            deleted = 0;

          // 1. IO Restoration
          for (const [path, snap] of conv.files) {
            await this._restore(path, snap);
            if (snap.existed) restored++; else deleted++;
          }

          log(`↩️ Undo conversation: restored ${restored}, deleted ${deleted}`, 'success');

          // 2. Ledger Update
          if (window.Deck0 && window.Deck0.actions) {
            window.Deck0.actions.session.resetPendingChanges();
          }

          this.conversations.delete(targetId);
          if (targetId === this.currentConversationId) {
            this.currentConversationId = null;
          }

          // 3. Explicit UI Refresh
          if (window.Deck0 && window.Deck0.ui) {
            if (window.Deck0.ui.tree && window.Deck0.ui.tree.refresh) await window.Deck0.ui.tree.refresh();
            if (window.Deck0.ui.files && window.Deck0.ui.files.render) await window.Deck0.ui.files.render();
            if (window.Deck0.ui.editor && window.Deck0.ui.editor.refreshOpenFiles) await window.Deck0.ui.editor.refreshOpenFiles();
          }

          return true;
        },

        // Get operation list of current conversation (for UI display)
        getOperations() {
          if (!this.currentConversationId) return [];
          const conv = this.conversations.get(this.currentConversationId);
          return conv ? conv.operations : [];
        }
      };

      // ===================== DECK0 EXECUTOR =====================
      // [TP-P4-2 Lite] Moved AFTER SnapshotManager - dependency order fixed
      // 执行层：快照/写盘/回滚/重放
      // - SnapshotManager: 自动快照与撤销 (now defined above)
      // - Executor: 唯一写盘入口（write/delete/去重/UI同步）

      const Executor = {
        // Atomic Write
        write: async (path, content) => {
          // 1. Snapshot (Safety) - SnapshotManager now always defined
          await SnapshotManager.snapshotFile(path);

          // 2. VFS Write (IO)
          await VfsManager.write(path, content);

          // 3. State Sync (Dedupe)
          store.addSyncPath(path);

          // 4. UI Sync
          scheduleUiSync('write', { forceEditor: true });

          return true;
        },

        // Atomic Delete
        delete: async (path) => {
          // 1. Snapshot (Safety) - SnapshotManager now always defined
          await SnapshotManager.snapshotFile(path);

          // 2. VFS Delete (IO)
          await VfsManager.del(path);

          // 3. State Sync (Dedupe)
          store.addSyncPath(path);

          // 4. UI Sync
          scheduleUiSync('delete', { forceEditor: true });

          return true;
        },

        // [TP-P3-5] Safe Restore IO (No Snapshot, No SyncList Pollution)
        restoreWrite: async (path, content) => {
          await VfsManager.write(path, content || '');
          return true;
        },

        restoreDelete: async (path) => {
          await VfsManager.del(path);
          return true;
        },

        // Atomic Replay (T6-2)
        applyActions: async (actions, mode = 'replay') => {
          log(`🔄 Executor: Applying ${actions.length} actions (Mode: ${mode})...`, 'purple');
          let success = 0;
          let failed = 0;

          for (const act of actions) {
            try {
              const type = (act.type || '').toUpperCase();
              const path = normalizeVfsPath(act.path);

              if (type === 'WRITE') {
                if (await Executor.write(path, act.content)) success++;
                else failed++;
              }
              else if (type === 'DELETE') {
                if (await Executor.delete(path)) success++;
                else failed++;
              }
              else if (type === 'PATCH') {
                let content = '';
                try {
                  content = await VfsManager.read(path);
                } catch (e) {
                  log(`⚠️ Replay Patch Skipped (Missing): ${path}`, 'warn');
                  failed++;
                  continue;
                }

                const patched = corePatch(content, act);
                if (patched !== null) {
                  if (await Executor.write(path, patched)) success++;
                  else failed++;
                } else {
                  log(`⚠️ Replay Patch Failed (No Match): ${path}`, 'warn');
                  failed++;
                }
              }
            } catch (e) {
              log(`❌ Replay Error: ${e.message}`, 'error');
              failed++;
            }
          }
          log(`✅ Replay Complete: ${success} success, ${failed} failed`, 'success');
          return { success, failed };
        }
      };

      const normalizeVfsPath = (path) => (path || '').trim().replace(/^`+|`+$/g, '').trim();
      const sanitizeOpPath = (rawPath) => {
        let p = normalizeVfsPath(rawPath);
        if (!p) return '';
        // Normalize slashes early
        p = p.replace(/\\/g, '/').trim();

        // Strip common "inline metadata" patterns if the model puts them on the same line
        p = p.replace(/\s+>.*$/, '').trim(); // markdown blockquote (Commit Msg, etc.)
        p = p.replace(/\s+\|.*$/, '').trim(); // pipe suffix

        // Strip common editor-like suffixes
        p = p.replace(/#L\d+(?:C\d+)?$/i, '').trim();
        p = p.replace(/:(\d+)(?::\d+)?$/i, '').trim(); // :line[:col]

        // Strip surrounding quotes/backticks (defensive)
        p = p.replace(/^["']+|["']+$/g, '').replace(/^`+|`+$/g, '').trim();

        // Trailing punctuation from prose
        p = p.replace(/[),.;]+$/g, '').trim();
        return p;
      };

      const isSafeVfsPath = (path) => {
        const p = normalizeVfsPath(path);
        if (!p) return false;
        if (/[<>"|?*\r\n\0]/.test(p)) return false;
        // Disallow absolute paths / traversal (AI ops should be repo-relative)
        if (p.startsWith('/') || p.startsWith('..') || p.includes('/../') || p.includes('\\..')) return false;
        // Windows-incompatible characters (keeps paths portable)
        if (/[:]/.test(p)) return false;
        return true;
      };
      const isTemplatePlaceholderPath = (path) => {
        const p = normalizeVfsPath(path).toLowerCase();
        if (!p) return true;
        if (p === 'path/to/file') return true;
        if (p === 'path/to/file.ext') return true;
        if (p.startsWith('path/to/file.')) return true;
        return false;
      };

      // ================= STREAMING PATCH PARSER (V18 - State Machine) =================
      const ParseState = {
        IDLE: 0,
        IN_PLAN: 1,
        IN_OP: 2,
        IN_SEARCH: 3,
        AFTER_SEARCH: 4,
        IN_REPLACE: 5
      };

      const StreamingPatchParser = {
        state: ParseState.IDLE,
        buffer: '',
        currentPlan: null,
        currentOp: null,
        currentSearch: '',
        currentReplace: '',
        executedOps: [],
        pendingPatches: [], // Queue of patches pending execution
        stats: {
          streamAttempted: 0,
          streamExecuted: 0
        },
        opTrace: [], // [V19.0 T6-1] Execution Trace
        _phase: 'stream', // 'stream' | 'replay'
        _inFlight: new Set(),
        _flushChain: Promise.resolve(),

        // Reset parser
        reset() {
          this.state = ParseState.IDLE;
          this.buffer = '';
          this.currentPlan = null;
          this.currentOp = null;
          this.currentSearch = '';
          this.currentReplace = '';
          this.executedOps = [];
          this.pendingPatches = [];
          this.stats = {
            streamAttempted: 0,
            streamExecuted: 0
          };
          this.opTrace = [];
          this._phase = 'stream';
          this._inFlight = new Set();
          this._flushChain = Promise.resolve();
          SnapshotManager.startConversation();
        },

        _track(p) {
          if (!p || typeof p.then !== 'function') return;
          this._inFlight.add(p);
          p.finally(() => this._inFlight.delete(p));
        },

        async _waitForInFlight() {
          if (!this._inFlight || this._inFlight.size === 0) return;
          await Promise.allSettled(Array.from(this._inFlight));
        },

        _scheduleFlush(planId) {
          const patches = this.pendingPatches.splice(0, this.pendingPatches.length);
          if (patches.length === 0) return;

          const run = async () => {
            log(`⏳ Executing ${patches.length} patches for ${planId}...`, 'purple');
            await this.flushPendingPatches(patches);
            log(`✅ All ${patches.length} patches executed`, 'success');
          };

          this._flushChain = this._flushChain.then(run).catch(e => {
            log(`❌ Patch execution error: ${e.message}`, 'error');
          });
          this._track(this._flushChain);
        },

        // Feed new stream data chunk
        feed(chunk) {
          this.buffer += chunk;
          this.parse();
        },

        // State machine parsing
        parse() {
          let iterations = 0;
          const maxIterations = 1000; // Prevent infinite loop

          while (iterations++ < maxIterations) {
            let consumed = false;

            // [V18.1 FIX] Safety check: If buffer has no newline and length is below safety threshold, wait for more data
            if (!this.buffer.includes('\n') && this.buffer.length < 100) {
              break;
            }

            // === IDLE: Waiting for PLAN_START ===
            if (this.state === ParseState.IDLE) {
              // Detect PLAN_START (requires full marker)
              const planMatch = this.buffer.match(/\[\[DECK0_PLAN_START:([\w\-]+)\]\]/);
              if (planMatch) {
                const idx = this.buffer.indexOf(planMatch[0]);
                this.buffer = this.buffer.slice(idx + planMatch[0].length);
                this.currentPlan = {
                  id: planMatch[1],
                  ops: []
                };
                this.state = ParseState.IN_PLAN;
                updatePlanStatus(planMatch[1], 'running');
                log(`▶️ Plan started: ${planMatch[1]}`, 'purple');
                consumed = true;
              }
            }

            // === IN_PLAN: Inside Plan, waiting for OP or PLAN_END ===
            else if (this.state === ParseState.IN_PLAN) {
              // Detect PLAN_END
              const endMatch = this.buffer.match(/\[\[DECK0_PLAN_END:([\w\-]+)\]\]/);
              if (endMatch) {
                const idx = this.buffer.indexOf(endMatch[0]);
                this.buffer = this.buffer.slice(idx + endMatch[0].length);

                // [V18.1 FIX] Asynchronous patch execution, do not block parsing (avoid cross-plan queue mixing)
                this._scheduleFlush(endMatch[1]);

                updatePlanStatus(endMatch[1], 'done');
                log(`✅ Plan completed: ${endMatch[1]}`, 'success');

                // [TP-P3-5] Record operation for Undo capability
                if (this.currentPlan) {
                  const affectedFiles = new Set(this.currentPlan.ops.map(o => o.path));
                  SnapshotManager.recordOperation(endMatch[1], affectedFiles);
                }

                this.executedOps.push(this.currentPlan);
                this.currentPlan = null;
                this.state = ParseState.IDLE;
                consumed = true;
                continue;
              }

              // [V18.1 FIX] Detect OP line - must end with newline to match, prevent partial match
              // Compatible with two formats:
              // 1) ### DECK0_OP: [PATCH] path/to/file
              // 2) ### DECK0_OP: PATCH path/to/file
              const opMatch = this.buffer.match(/###\s*DECK0_OP:\s*(?:\[(WRITE|PATCH|DELETE)\]|(WRITE|PATCH|DELETE))\s*([^\n\r]+)\r?\n/i);
              if (opMatch) {
                const idx = this.buffer.indexOf(opMatch[0]);
                this.buffer = this.buffer.slice(idx + opMatch[0].length);
                const opType = (opMatch[1] || opMatch[2] || '').toUpperCase();
                const opPath = sanitizeOpPath(opMatch[3] || '');
                const skip = isTemplatePlaceholderPath(opPath) || !isSafeVfsPath(opPath);
                this.currentOp = {
                  type: opType,
                  path: opPath,
                  patches: [],
                  newContent: null,
                  _skip: skip
                };
                this.state = ParseState.IN_OP;
                if (skip) log(`⚠️ Skipping invalid op: ${opType} ${opPath || '(empty path)'}`, 'warn');
                else log(`📝 Op detected: ${opType} ${opPath}`, 'info');

                // 自动快照该文件
                if (!skip) SnapshotManager.snapshotFile(opPath);
                consumed = true;
              }
            }

            // === IN_OP: 在操作内，等待代码块或 SEARCH ===
            else if (this.state === ParseState.IN_OP) {
              // 检测下一个 OP 或 PLAN_END（当前 OP 结束）
              const nextOpMatch = this.buffer.match(/(?:^|\n)[ \t]*###\s*DECK0_OP:\s*/i);
              const planEndMatch = this.buffer.match(/\[\[DECK0_PLAN_END:/);

              // [V18.1 FIX] 检测 Git-Fence SEARCH 开始 - 必���有完整的开始行
              const searchMatch = this.buffer.match(/(?:\n|^)[ \t]*<<<<<<[ \t]*SEARCH[ \t]*\r?\n/i);
              if (searchMatch) {
                const idx = this.buffer.indexOf(searchMatch[0]);
                this.buffer = this.buffer.slice(idx + searchMatch[0].length);
                this.currentSearch = '';
                this.state = ParseState.IN_SEARCH;
                consumed = true;
                continue;
              }

              // 检测代码块（用于 WRITE 操作）
              const codeBlockMatch = this.buffer.match(/```(\w*)\r?\n/);
              if (codeBlockMatch && this.currentOp && this.currentOp.type === 'WRITE') {
                const startIdx = this.buffer.indexOf(codeBlockMatch[0]) + codeBlockMatch[0].length;
                const endMatch = this.buffer.slice(startIdx).match(/\n```/);
                // If the closing fence hasn't arrived yet, wait for more data.
                // This prevents "### *_OP" lines inside the code block from prematurely ending the op.
                if (!endMatch) break;
                if (endMatch) {
                  const code = this.buffer.slice(startIdx, startIdx + endMatch.index);
                  this.buffer = this.buffer.slice(startIdx + endMatch.index + endMatch[0].length);
                  this.currentOp.newContent = code;

                  // Execute WRITE immediately
                  if (!this.currentOp._skip) this._track(this.executeWrite(this.currentOp));

                  if (this.currentPlan && !this.currentOp._skip) this.currentPlan.ops.push(this.currentOp);
                  this.currentOp = null;
                  this.state = ParseState.IN_PLAN;
                  consumed = true;
                  continue;
                }
              }

              // If next OP or PLAN_END is detected, the current OP ends
              if (nextOpMatch || planEndMatch) {
                if (this.currentOp && !this.currentOp._skip) {
                  if (this.currentOp.type === 'DELETE') this._track(this.executeDelete(this.currentOp));
                  if (this.currentPlan) this.currentPlan.ops.push(this.currentOp);
                }
                this.currentOp = null;
                this.state = ParseState.IN_PLAN;
                consumed = true;
                continue;
              }
            }

            // === IN_SEARCH: 读取 SEARCH 内容 ===
            else if (this.state === ParseState.IN_SEARCH) {
              // [V18.1 FIX] 检测 ====== 分隔符 - ��须有完整的分隔行
              const sepMatch = this.buffer.match(/(?:\n|^)[ \t]*======[ \t]*\r?\n/);
              if (sepMatch) {
                const idx = this.buffer.indexOf(sepMatch[0]);
                // 去掉 SEARCH 内容末尾的换行符
                this.currentSearch = this.buffer.slice(0, idx);
                this.buffer = this.buffer.slice(idx + sepMatch[0].length);
                this.currentReplace = '';
                this.state = ParseState.IN_REPLACE;
                consumed = true;
              }
            }

            // === IN_REPLACE: 读取 REPLACE 内容 ===
            else if (this.state === ParseState.IN_REPLACE) {
              // [V18.1 FIX] 检测 >>>>>> REPLACE 结束符 - 必须有完整的结束行
              const endMatch = this.buffer.match(/(?:\n|^)[ \t]*>>>>>>[ \t]*REPLACE[ \t]*(?:\r?\n|$)/i);
              if (endMatch) {
                const idx = this.buffer.indexOf(endMatch[0]);
                // 去掉 REPLACE 内容末尾的换���符
                this.currentReplace = this.buffer.slice(0, idx);
                this.buffer = this.buffer.slice(idx + endMatch[0].length);

                // 添加到待执行队列
                if (this.currentOp && !this.currentOp._skip) {
                  this.pendingPatches.push({
                    path: this.currentOp.path,
                    search: this.currentSearch,
                    replace: this.currentReplace
                  });
                  this.currentOp.patches.push({
                    search: this.currentSearch,
                    replace: this.currentReplace
                  });
                  // log(`📋 Patch queued: ${this.currentOp.path}`, 'info'); // [V18.2] Reduced noise
                }

                this.currentSearch = '';
                this.currentReplace = '';
                this.state = ParseState.IN_OP;
                consumed = true;
              }
            }

            if (!consumed) break;
          }
        },

        // 执行 WRITE 操作
        async executeWrite(op) {
          if (this._phase === 'stream') this.stats.streamAttempted++;
          try {
            // [Fix] Sanitize null content to empty string (e.g. empty .gitkeep)
            const content = (op.newContent === null || op.newContent === undefined) ? '' : op.newContent;
            // [TP-P3-4] Use Executor.write (includes snapshot/syncList/UI sync)
            const ok = await Executor.write(op.path, content);
            if (!ok) {
              log(`❌ Write failed: ${op.path}`, 'error');
              return false;
            }
            // [TP-P3-4] Removed manual addSyncPath (Executor handles it)
            log(`✍️ Written: ${op.path}`, 'success');
            // [TP-P3-4] Removed manual renderChangedFiles (Executor triggers scheduleUiSync)
            if (this._phase === 'stream') {
              this.stats.streamExecuted++;
              this.opTrace.push({ type: 'WRITE', path: op.path }); // [T6-1] Trace
            }
            return true;
          } catch (e) {
            log(`❌ Write failed: ${op.path} - ${e.message}`, 'error');
            return false;
          }
        },

        // 执行 DELETE 操作
        async executeDelete(op) {
          if (this._phase === 'stream') this.stats.streamAttempted++;
          try {
            let exists = true;
            try {
              await readFileContent(op.path);
            } catch (e) {
              exists = false;
            }

            // [TP-P3-4] Idempotency: already deleted => still need to mark in syncList
            if (!exists) {
              Deck0Actions.session.addSyncPath(op.path);
              log(`🗑️ Already deleted: ${op.path}`, 'success');
              if (this._phase === 'stream') {
                this.stats.streamExecuted++;
                this.opTrace.push({ type: 'DELETE', path: op.path }); // [T6-1] Trace
              }
              return true;
            }

            // [TP-P3-4] Use Executor.delete (includes snapshot/syncList/UI sync)
            const ok = await Executor.delete(op.path);
            if (ok) {
              // [TP-P3-4] Removed manual addSyncPath (Executor handles it)
              log(`🗑️ Deleted: ${op.path}`, 'success');
              // [TP-P3-4] Removed manual renderChangedFiles (Executor triggers scheduleUiSync)
              if (this._phase === 'stream') {
                this.stats.streamExecuted++;
                this.opTrace.push({ type: 'DELETE', path: op.path }); // [T6-1] Trace
              }
              return true;
            }
            log(`⚠️ Delete failed: ${op.path}`, 'warn');
            return false;
          } catch (e) {
            log(`❌ Delete failed: ${op.path} - ${e.message}`, 'error');
            return false;
          }
        },

        // 执行单个 PATCH
        async executePatch(patch, silent = false) {
          if (this._phase === 'stream') this.stats.streamAttempted++;
          const markSuccess = () => {
            if (this._phase === 'stream') {
              this.stats.streamExecuted++;
              this.opTrace.push({ type: 'PATCH', path: patch.path }); // [T6-1] Trace
            }
          };
          try {
            if (!silent) log(`🔧 Patching: ${patch.path}`, 'info');
            let content = '';
            try {
              content = await readFileContent(patch.path);
            } catch (e) {
              const msg = (e && e.message) ? e.message : String(e);
              const name = (e && e.name) ? String(e.name) : '';
              const isMissing = /not found/i.test(msg) || /notfound/i.test(name) || /ENOENT/i.test(msg);
              if (isMissing) {
                log(`⚠️ Patch skipped (file missing): ${patch.path}`, 'warn');
                return false;
              }
              throw e;
            }
            const action = {
              searchBlock: patch.search,
              replaceBlock: patch.replace
            };

            // Prefer the same patch engine as Batch Sim (strict -> trimmed -> flex-indent)
            let newContent = null;
            try {
              if (typeof corePatch === 'function') {
                newContent = corePatch(content, action);
              }
            } catch (e) { }

            // Fallback (should be rare)
            if (newContent === null) {
              if (content.includes(action.searchBlock)) newContent = content.replace(action.searchBlock, action.replaceBlock);
              else {
                const trimmedSearch = (action.searchBlock || '').trim();
                if (trimmedSearch && content.includes(trimmedSearch)) newContent = content.replace(trimmedSearch, action.replaceBlock);
              }
            }

            // Idempotency: treat as success if already applied
            if (newContent === null) {
              const replaceBlock = action.replaceBlock || '';
              const trimmedReplace = replaceBlock.trim();
              if (replaceBlock && replaceBlock.length > 0 && content.includes(replaceBlock)) {
                if (!silent) log(`✅ Patch already applied: ${patch.path}`, 'success');
                markSuccess();
                return true;
              }
              if (trimmedReplace && content.includes(trimmedReplace)) {
                if (!silent) log(`✅ Patch already applied (trimmed): ${patch.path}`, 'success');
                markSuccess();
                return true;
              }

              log(`⚠️ Patch search not found: ${patch.path}`, 'warn');
              log(`📋 Search preview: ${(action.searchBlock || '').slice(0, 80)}...`, 'info');
              return false;
            }

            // No-op
            if (newContent === content) {
              if (!silent) log(`✅ Patch no-op: ${patch.path}`, 'success');
              markSuccess();
              return true;
            }

            // [TP-P3-4] Use Executor.write (includes snapshot/syncList/UI sync)
            const ok = await Executor.write(patch.path, newContent);
            if (!ok) return false;
            // [TP-P3-4] Removed manual addSyncPath (Executor handles it)
            if (!silent) log(`✅ Patched: ${patch.path}`, 'success');
            // [TP-P3-4] Removed manual renderChangedFiles (Executor triggers scheduleUiSync)
            markSuccess();
            return true;
          } catch (e) {
            log(`❌ Patch failed: ${patch.path}`, 'error');
            log(`📋 Error: ${e.message}`, 'error');
            return false;
          }
        },

        // Flush pending patches queue (called at Plan end)
        async flushPendingPatches(patches = null) {
          const list = patches || this.pendingPatches;
          const patchStats = new Map(); // path -> count

          for (const patch of list) {
            const success = await this.executePatch(patch, true);
            if (success) {
              patchStats.set(patch.path, (patchStats.get(patch.path) || 0) + 1);
            }
          }

          patchStats.forEach((count, path) => {
            log(`🔧 Patched: ${path} (${count} ${count > 1 ? 'hunks' : 'hunk'})`, 'success');
          });

          if (!patches) this.pendingPatches = [];
        },

        // Final correction (called at stream end)
        async finalCorrection(fullText) {
          log('🔍 Running final correction...', 'purple');

          // Wait for streaming execution to finish (writes/deletes/plan-end flushes)
          await this._waitForInFlight();

          if (!fullText || typeof fullText !== 'string' || fullText.length < 10) return;
          if (typeof parseActions !== 'function') return;

          const planBlockRegex = /\[\[DECK0_PLAN_START:([\w\-]+)\]\]([\s\S]*?)\[\[DECK0_PLAN_END:\1\]\]/g;
          let m;
          const actions = [];

          const updatedPlans = [];
          const existingPlans = store.getState().protocol.plans;

          while ((m = planBlockRegex.exec(fullText)) !== null) {
            const planId = m[1];
            const block = m[2];
            const parsed = parseActions(block) || [];
            for (const act of parsed) actions.push(act);

            const existing = existingPlans.find(p => p.id === planId);
            if (existing) {
              updatedPlans.push({ ...existing, ops: parsed });
            }
          }

          const fullCount = actions.length;
          if (fullCount === 0) return;

          // [T6-1] Strict EOS Verification (Signature Match)
          // Compare executed opTrace vs parsed actions
          const verify = () => {
            if (this.stats.streamExecuted !== fullCount) return { ok: false, msg: `Count Mismatch: ${this.stats.streamExecuted} vs ${fullCount}` };

            // Check Order & Signatures
            for (let i = 0; i < fullCount; i++) {
              const exp = actions[i];
              const act = this.opTrace[i];
              if (!act) return { ok: false, msg: `Missing op at index ${i}` };

              const expPath = normalizeVfsPath(exp.path);
              const actPath = normalizeVfsPath(act.path);
              if (expPath !== actPath) return { ok: false, msg: `Path Mismatch [${i}]: ${actPath} != ${expPath}` };

              const expType = exp.type.toUpperCase();
              const actType = act.type.toUpperCase();
              if (expType !== actType) return { ok: false, msg: `Type Mismatch [${i}]: ${actType} != ${expType}` };
            }
            return { ok: true, msg: 'Signatures Matched' };
          };

          const result = verify();
          if (result.ok) {
            Deck0Actions.protocol.updatePlans(updatedPlans);
            log(`✅ Stream execution verified (${fullCount} ops).`, 'success');
            return;
          }

          log(`⚠️ EOS Mismatch: ${result.msg}. Auto-undo + replay...`, 'warn');

          // 1) Undo partial streaming execution
          try {
            const convId = SnapshotManager.currentConversationId;
            if (convId) await SnapshotManager.undoConversation(convId);
          } catch (e) {
            log(`❌ Auto-undo failed: ${e.message}`, 'error');
          }

          // Reset changed list for accurate re-run
          Deck0Actions.session.resetPendingChanges();
          renderChangedFiles();

          // 2) Replay full execution from the completed response
          this._phase = 'replay';
          SnapshotManager.startConversation();

          if (window.Executor && window.Executor.applyActions) {
            // [V19.0] Atomic Replay via Executor
            await window.Executor.applyActions(actions, 'replay');
            Deck0Actions.protocol.updatePlans(updatedPlans); // Sync Store with corrected plans
          } else {
            // Legacy Replay Fallback
            let replayOk = 0;
            const correctionStats = new Map(); // path -> count

            for (const act of actions) {
              if (!act || !act.type || !act.path) continue;
              const path = normalizeVfsPath(act.path);
              if (isTemplatePlaceholderPath(path)) continue;

              try {
                await SnapshotManager.snapshotFile(path);
                if (act.type.toLowerCase() === 'patch') {
                  if (await this.executePatch({
                    path,
                    search: act.searchBlock,
                    replace: act.replaceBlock
                  }, true)) {
                    replayOk++;
                    correctionStats.set(path, (correctionStats.get(path) || 0) + 1);
                  }
                } else if (act.type === 'write') {
                  // [TP-P3-4] Use Executor.write (includes snapshot/syncList/UI sync)\n                  const ok = await Executor.write(path, act.content);
                  if (ok) {
                    replayOk++;
                    // [TP-P3-4] Removed manual addSyncPath (Executor handles it)
                    log(`✍️ Written: ${path}`, 'success');
                  }
                } else if (act.type === 'delete') {
                  if (await this.executeDelete({
                    path
                  })) replayOk++;
                }
              } catch (e) {
                log(`❌ Replay failed: ${act.type} ${path} - ${e.message}`, 'error');
              }
            }
            log(`🏁 Replay completed: ${replayOk}/${fullCount}`, replayOk === fullCount ? 'success' : 'warn');
          }

          this._phase = 'stream';
          await renderChangedFiles();
        }
      };

      if (!window.originalXHR) window.originalXHR = window.XMLHttpRequest;

      class PhantomXHR extends window.originalXHR {
        constructor() {
          super();
          this._isTarget = false;
          this._processedOffset = 0;
          this._runId = 0;
        }

        open(method, url) {
          this._monitorUrl = url;
          const canCheck = url && typeof url.includes === 'function';
          this._isTarget = !!(canCheck && DECK0_NET_HINTS.XHR_TARGET_URL_HINTS.some(h => url.includes(h)));
          return super.open(method, url);
        }

        send(body) {
          // [V17.0] Automation Check
          const shouldInject = (Deck0.store.getState().settings.interactionMode === 'PROTOCOL');

          if (this._isTarget && shouldInject) {
            this._runId = Deck0.store.getState().runtime.runId || 0;
            Deck0.store.mutate('runtime', r => { r.activeStreamOwner = this; });
            // Reset Stream State
            this._processedOffset = 0;

            // [V19.0] Bridge Signal: notify the new kernel that a stream is starting.
            // ProtocolEngine owns all reset/initialization work (Store + StreamingPatchParser + Snapshot session).
            try {
              if (window.Transport && typeof window.Transport.emit === 'function') {
                window.Transport.emit('START', { runId: this._runId });
              }
            } catch (e) { }

            try {
              let payload = null;
              let isJson = false;
              let isForm = false;

              if (typeof body === 'string') {
                if (body.startsWith('[')) {
                  payload = JSON.parse(body);
                  isJson = true;
                } else if (body.includes('f.req=')) {
                  const params = new URLSearchParams(body);
                  payload = JSON.parse(params.get('f.req'));
                  isForm = true;
                }
              }

              if (payload) {
                // 0. INJECT MODEL (User Request)
                if (Deck0.store.getState().settings.model) {
                  payload[0] = `models/${Deck0.store.getState().settings.model}`;
                  log(`🤖 Model Injected: ${Deck0.store.getState().settings.model}`, 'info');
                }

                // 1. INJECT SYSTEM PROMPT
                if (!payload[5]) payload[5] = [
                  [
                    [null, ""]
                  ], "user"
                ];
                if (payload[5] && payload[5][0] && payload[5][0][0]) {
                  payload[5][0][0][1] = getSystemPrompt();
                  log('🛡️ System Prompt Injected', 'purple');
                }

                // 2. INJECT PROJECT CONTEXT (HUD Automation)
                if (_contextBuffer && shouldInject) {
                  // We need to find the user prompt in the payload. 
                  // It's usually in payload[0][0]... or we can use JSON.stringify/replace technique
                  // But replacing inside the structure is safer.
                  // Heuristic: The user prompt is a String, and usually the last user input.
                  // But we don't know the exact path in the massive array.
                  // FALLBACK: String replacement in the serialized body is risky but effective if key is unique.
                  // BETTER: We injected via 'prepareDeck0Payload', we know the textarea value logic.
                  // Let's iterate payload[0] to find the user string.

                  const findAndPrepend = (arr) => {
                    for (let i = 0; i < arr.length; i++) {
                      if (typeof arr[i] === 'string' && arr[i].length > 0) {
                        // This is a candidate. In Gemini, user prompt is usually the first non-null string in a specific tuple.
                        // Let's just prepend to the FIRST string found in the request block (usually [0][0][0])
                        // Actually, let's just use the fact that we hijacked the flow.
                        // We can replace the *Textarea Value* if we knew it.
                        // Wait, we can read the textarea value right now!
                        const ta = document.querySelector('textarea');
                        if (ta && ta.value && arr[i] === ta.value) {
                          arr[i] = _contextBuffer + "\n\n" + arr[i];
                          return true;
                        }
                        // If textarea was cleared, we might fail.
                        // But 'send' happens before UI update usually.
                      }
                      if (Array.isArray(arr[i])) {
                        if (findAndPrepend(arr[i])) return true;
                      }
                    }
                    return false;
                  };

                  // Try deep search
                  if (payload[0]) findAndPrepend(payload[0]);

                  // Clear buffer
                  _contextBuffer = null;
                  log('📂 Project Context Injected (Prefix)', 'purple');
                }

                // Re-serialize
                if (isJson) body = JSON.stringify(payload);
                else if (isForm) {
                  const p = new URLSearchParams(body);
                  p.set('f.req', JSON.stringify(payload));
                  body = p.toString();
                }
              }
            } catch (e) {
              log('Injection Warning: ' + e.message, 'warn');
            }

            // [V18] 设置流结束监听
            this._setupLoadListener();
          }
          return super.send(body);
        }

        _getModifiedText() {
          const currentRaw = super.responseText;
          if (this._isTarget && Deck0.store.getState().settings.interactionMode === 'PROTOCOL') {
            const newBytes = currentRaw.slice(this._processedOffset);
            this._processedOffset = currentRaw.length;
            if (newBytes && window.Transport && typeof window.Transport.emit === 'function') {
              window.Transport.emit('RAW_FRAME', newBytes);
            }
          }
          return currentRaw;
        }

        get responseText() {
          return this._getModifiedText();
        }
        get response() {
          // 触发解析逻辑，但返回原始响应
          if (this._isTarget) this._getModifiedText();
          return super.response;
        }

        // [V18] 监听流结束，执行最终校正
        _setupLoadListener() {
          this.addEventListener('loadend', async () => {
            if (this._isTarget && Deck0.store.getState().settings.interactionMode === 'PROTOCOL') {
              // [V19.0] EOS fallback: if provider misses EOS, trigger finalize via Transport.
              // Keep runId binding to avoid cross-run interference.
              let rid = 0;
              try { rid = this._runId || (Deck0.store.getState().runtime ? (Deck0.store.getState().runtime.runId || 0) : 0); } catch (e) { rid = 0; }
              try {
                if (Deck0.store.getState().runtime && Deck0.store.getState().runtime.finalizeDoneRunId === rid) return;
              } catch (e) { }

              try {
                if (window.Transport && typeof window.Transport.emit === 'function') {
                  window.Transport.emit('EOS', { runId: rid, fallback: true });
                } else if (typeof BridgeUIAdapter !== 'undefined' && BridgeUIAdapter && BridgeUIAdapter.handleEOS) {
                  await BridgeUIAdapter.handleEOS({ runId: rid, fallback: true });
                }
              } catch (e) {
                console.warn('[PhantomXHR] loadend EOS fallback failed', e);
              }
            }
          }, { once: true });
        }
      }

      // Install PhantomXHR
      window.XMLHttpRequest = PhantomXHR;
      if (!window.originalFetch) window.originalFetch = window.fetch;
      const parseDeck0Protocol = (md) => {
        if (!Deck0.store.getState().protocol.manifestParsed) {
          const manifestMatch = md.match(/\[\[DECK0_MANIFEST_START\]\]([\s\S]*?)\[\[DECK0_MANIFEST_END\]\]/);
          if (manifestMatch) {
            try {
              // [UX FIX]: Set badge to GENERATING (Yellow/Green)
              const badge = Deck0.ui.refs.statusBadge;
              if (badge) {
                badge.textContent = 'GENERATING';
                badge.style.background = '';
                badge.classList.remove('deck0-badge-thinking');
                badge.classList.add('deck0-badge-generating');
              }

              const manifest = JSON.parse(manifestMatch[1].trim());
              totalPlansInManifest = manifest.plans.length;
              completedPlansCounter = 0;
              log(`Manifest Parsed: ${manifest.plans.length} Plans`, 'purple');

              // [TP-P1-DECOUPLE-FIX] Relocated: Format plans and commit to Store
              const formattedPlans = manifest.plans.map(p => ({
                ...p,
                status: 'pending'
              }));
              Deck0Actions.protocol.setPlansIfEmpty(formattedPlans);

              renderManifest();
              Deck0Actions.protocol.setManifestParsed(true);

              // Edge case: If manifest has 0 plans (rare), ready immediately
              if (totalPlansInManifest === 0) {
                if (badge) {
                  badge.classList.remove('deck0-badge-thinking');
                  badge.classList.remove('deck0-badge-generating');
                }
                if (updateSendBtnState) updateSendBtnState(false);
                setBadgeState('READY', THEME.success);
              }
            } catch (e) {
              log('Manifest Parse Error: ' + e.message, 'error');
              setBadgeState('ERROR', THEME.danger);
            }
          }
        }
        if (Deck0.store.getState().protocol.manifestParsed) {
          let m;
          const planStartRegex = /\[\[DECK0_PLAN_START:(.*?)\]\]/g;
          while ((m = planStartRegex.exec(md)) !== null) updatePlanStatus(m[1].trim(), 'running');
          const planEndRegex = /\[\[DECK0_PLAN_END:(.*?)\]\]/g;
          while ((m = planEndRegex.exec(md)) !== null) updatePlanStatus(m[1].trim(), 'done');
        }
      };

      const getBaselineForPath = async (path) => {
        const p = normalizeVfsPath(path);
        try {
          const convId = SnapshotManager.currentConversationId;
          if (convId) {
            const conv = SnapshotManager.conversations.get(convId);
            const snap = conv && conv.files ? conv.files.get(p) : null;
            if (snap) {
              return {
                existed: !!snap.existed,
                content: snap.content || ''
              };
            }
          }
        } catch (e) { }

        if (!Array.isArray(Deck0.store.getState().project.baselineSnapshot)) Deck0.store.mutate('project', p => { p.baselineSnapshot = []; });
        const rec = (Deck0.store.getState().project.baselineSnapshot || []).find(f => normalizeVfsPath(f.path) === p);
        if (rec) {
          return {
            existed: true,
            content: rec.content || ''
          };
        }

        // [V18.7] Fallback: Read from disk (Lazy Repair)
        // If we don't have a baseline snapshot, assume the current disk content 
        // (at this moment) is the baseline we should have captured.
        try {
          const diskContent = await readFileContent(p);
          // Cache it so we don't read disk every time
          Deck0.store.mutate('project', proj => { if (proj.baselineSnapshot) proj.baselineSnapshot.push({ path: p, content: diskContent }); });
          log(`[DEBUG] Baseline recovered from disk for ${p}`, 'grey');
          return { existed: true, content: diskContent };
        } catch (e) {
          // File truly doesn't exist on disk
          // console.warn(`[DEBUG] No baseline and file missing for ${p}. Treated as new.`);
          return { existed: false, content: '' };
        }
      };

      const exitEditorViewMode = async () => {
        Deck0Actions.legacy.clearView();
        try {
          updateEditorModeBar();
        } catch (e) { }
        try {
          if (EditorManager && typeof EditorManager.refreshOpenFiles === 'function') await EditorManager.refreshOpenFiles();
        } catch (e) { }

        // [V18.11] Full UI Reset
        renderExecutionLog(); // Clear Timeline Highlight
        await refreshFileTree(true); // Restore Explorer (Remove Dimming/Badges)
      };

      const enterDiffReviewMode = async (focusPath = null, opts = {}) => {
        // [V18.9 FIX] GPT-5.2 Enhanced Logic: Auto-select first diffable file
        const paths = [...new Set((Deck0.store.getState().session.syncList || []).map(p => normalizeVfsPath(p)).filter(Boolean))];
        if (paths.length === 0) {
          log('No pending changes to review.', 'info');
          return false;
        }

        if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode === 'time_travel') return false;

        const baseMap = new Map();
        const nextMap = new Map();
        const statusMap = new Map();
        const changed = [];

        for (const p0 of paths) {
          const p = normalizeVfsPath(p0);
          if (!p || !isSafeVfsPath(p)) continue;

          const base = await getBaselineForPath(p);
          const baseExists = !!(base && base.existed);
          const baseContent = String((base && base.content) || '');

          let nextExists = true;
          let nextContent = '';
          try {
            nextContent = await readFileContent(p);
          } catch (e) {
            nextExists = false;
          }

          if (!baseExists && nextExists) {
            baseMap.set(p, null);
            nextMap.set(p, String(nextContent || ''));
            statusMap.set(p, 'added');
            changed.push(p);
          } else if (baseExists && !nextExists) {
            baseMap.set(p, baseContent);
            nextMap.set(p, null);
            statusMap.set(p, 'deleted');
            changed.push(p);
          } else if (baseExists && nextExists) {
            baseMap.set(p, baseContent);
            nextMap.set(p, String(nextContent || ''));
            const st = baseContent === String(nextContent || '') ? 'unchanged' : 'modified';
            statusMap.set(p, st);
            if (st !== 'unchanged') changed.push(p);
          }
        }

        if (changed.length === 0) {
          log('No diffs detected (all files unchanged).', 'info');
          return false;
        }

        // GPT-5.2 Smart Focus Selection
        const focus = focusPath ? normalizeVfsPath(focusPath) : null;
        const finalFocus = (focus && statusMap.has(focus) && statusMap.get(focus) !== 'unchanged') ? focus : changed[0];

        Deck0Actions.legacy.setView({
          mode: 'diff_review',
          preview: true,
          readOnly: false,
          baseMap,
          virtualMap: nextMap,
          statusMap,
          confirmText: 'Close',
          cancelText: 'Close',
          confirmBg: '#333',
          onConfirm: exitEditorViewMode,
          onCancel: exitEditorViewMode
        });

        updateEditorModeBar();
        await refreshFileTree(true); // Apply diff focus

        try {
          if (finalFocus) {
            await EditorManager.open(finalFocus);
            console.log(`[Auto-Diff] Opened: ${finalFocus}`);
            return true;
          }
        } catch (e) {
          if (!opts || !opts.silent) log(`Auto-diff open failed: ${e.message}`, 'error');
        }
        return false;
      };

      const renderChangedFiles = async () => {
        // [TRANSITION] Shadow UI Bridge
        if (Deck0.ui.shadow?.enabled && Deck0.ui.shadow.renderChangedFiles) {
          Deck0.ui.shadow.renderChangedFiles();
        }
        Deck0Actions.ui.incrementRenderSeq();
        const seq = Deck0.store.getState().ui.renderSeq;
        const list = Deck0.ui.refs.filesBody;
        if (!list) return;
        list.replaceChildren();

        const cleanPath = (p) => String(p || '').replace(/\\/g, '/').replace(/^\.?\/*/, '').trim();
        const getFileName = (p) => {
          const parts = cleanPath(p).split('/').filter(Boolean);
          return parts.length ? parts[parts.length - 1] : p;
        };
        const getDirName = (p) => {
          const parts = cleanPath(p).split('/').filter(Boolean);
          if (parts.length <= 1) return '';
          return parts.slice(0, -1).join('/');
        };

        const computeLineStats = (oldText, newText) => {
          const diffs = DiffEngine.compute(oldText || '', newText || '');
          let added = 0;
          let removed = 0;
          for (const d of diffs) {
            if (d.type === 'add') added++;
            else if (d.type === 'del') removed++;
          }
          return {
            added,
            removed
          };
        };

        const paths = [...new Set(Deck0.store.getState().session.syncList)].map(cleanPath).filter(Boolean);
        if (filesCount) filesCount.textContent = `CHANGED FILES (${paths.length})`;

        if (paths.length === 0) {
          el('div', {
            text: 'No changes yet.',
            style: {
              padding: '10px',
              color: '#666',
              fontSize: '10px',
              textAlign: 'center'
            },
            parent: list
          });
          return;
        }

        // [V18.9] Auto-Expand Accordion
        const filesBody = Deck0.ui.refs.filesBody;
        if (filesBody && !filesBody.classList.contains('open')) {
          filesBody.classList.add('open');
          const acc = filesBody.closest(DECK0_SELECTORS.UI_ACCORDION);
          if (acc) {
            const arrow = acc.querySelector(DECK0_SELECTORS.UI_ACC_ARROW);
            if (arrow) arrow.style.transform = 'rotate(0deg)';
          }
        }

        const infos = await Promise.all(paths.map(async (p) => {
          const baseline = await getBaselineForPath(p);
          let exists = true;
          let content = '';
          try {
            content = await readFileContent(p);
          } catch (e) {
            exists = false;
            content = '';
          }

          const op = !exists ? 'delete' : (baseline.existed ? 'patch' : 'write');

          // [DEBUG] Log diff stats (opt-in; noisy for non-dev users)
          if (DECK0_DEBUG.RENDER_CHANGED_FILES_VERBOSE) {
            if (baseline.existed) {
              const baseLen = (baseline.content || '').length;
              const currLen = (content || '').length;
              if (Math.abs(baseLen - currLen) > 1000) {
                log(`[DEBUG] Large Diff detected for ${p}: Base(${baseLen}) vs Curr(${currLen})`, 'warn');
              }
            } else {
              log(`[DEBUG] Baseline missing for ${p} (Op: ${op})`, 'warn');
            }
          }

          const stats = computeLineStats(baseline.content, content);
          return {
            path: p,
            op,
            exists,
            fileName: getFileName(p),
            dirName: getDirName(p),
            linesAdded: stats.added,
            linesRemoved: stats.removed
          };
        }));

        if (seq !== Deck0.store.getState().ui.renderSeq) return;

        // [V18.9] Smart Auto-Focus Calculation
        try {
          // Find first file that is either a DELETE or has actual content changes
          const first = infos.find(f => f && f.path && (f.op === 'delete' || (Number(f.linesAdded) || 0) + (Number(f.linesRemoved) || 0) > 0));
          _firstDiffablePath = first ? first.path : null;
        } catch (e) {
          _firstDiffablePath = null;
        }

        const totalAdded = infos.reduce((sum, f) => sum + (Number(f.linesAdded) || 0), 0);
        const totalRemoved = infos.reduce((sum, f) => sum + (Number(f.linesRemoved) || 0), 0);
        const title = infos.length === 1 ? '1 file changed' : `${infos.length} files changed`;

        const card = el('div', {
          cls: 'deck0-files-card',
          parent: list
        });
        const header = el('div', {
          cls: 'deck0-files-card-row',
          parent: card
        });

        el('div', {
          cls: 'deck0-files-card-title',
          text: title,
          parent: header
        });

        const meta = el('div', {
          cls: 'deck0-files-card-meta',
          parent: header
        });
        const stats = el('div', {
          cls: 'deck0-files-card-stats',
          parent: meta
        });
        el('span', {
          cls: 'deck0-files-stat-add',
          text: `+${totalAdded}`,
          parent: stats
        });
        el('span', {
          cls: 'deck0-files-stat-del',
          text: `-${totalRemoved}`,
          parent: stats
        });

        const actions = el('div', {
          cls: 'deck0-files-card-actions',
          parent: meta
        });
        el('div', {
          cls: 'deck0-files-action-btn keep',
          text: 'KEEP',
          parent: actions,
          click: (e) => {
            e.preventDefault();
            e.stopPropagation();
            keepAllChanges();
          }
        });
        el('div', {
          cls: 'deck0-files-action-btn',
          text: 'UNDO',
          parent: actions,
          click: (e) => {
            e.preventDefault();
            e.stopPropagation();
            undoChanges();
          }
        });

        const opIcon = (op) => (op === 'delete' ? '🗑️' : (op === 'write' ? '✨' : '📝'));

        infos.forEach((f) => {
          const row = el('div', {
            cls: `deck0-changed-file-row ${f.op === 'delete' ? 'deleted' : ''}`,
            parent: card,
            click: async (e) => {
              e.preventDefault();
              e.stopPropagation();
              try {
                await enterDiffReviewMode(f.path);
              } catch (e) { }
            }
          });

          const main = el('div', {
            cls: 'deck0-changed-file-main',
            parent: row
          });
          el('div', {
            cls: 'deck0-changed-file-icon',
            text: opIcon(f.op),
            parent: main
          });
          el('div', {
            cls: 'deck0-changed-file-name',
            text: f.fileName,
            parent: main
          });
          if (f.dirName) el('div', {
            cls: 'deck0-changed-file-dir',
            text: f.dirName,
            parent: main
          });

          const right = el('div', {
            cls: 'deck0-changed-file-right',
            parent: row
          });
          const perStats = el('div', {
            cls: 'deck0-changed-file-stats',
            parent: right
          });
          el('span', {
            cls: 'deck0-files-stat-add',
            text: `+${Number(f.linesAdded) || 0}`,
            parent: perStats
          });
          el('span', {
            cls: 'deck0-files-stat-del',
            text: `-${Number(f.linesRemoved) || 0}`,
            parent: perStats
          });

          const perActions = el('div', {
            cls: 'deck0-changed-file-actions',
            parent: right
          });
          el('div', {
            cls: 'deck0-btn-mini keep',
            text: '✓',
            attrs: {
              title: 'Keep'
            },
            parent: perActions,
            click: (e) => {
              e.preventDefault();
              e.stopPropagation();
              keepFileChange(f.path);
            }
          });
          el('div', {
            cls: 'deck0-btn-mini undo',
            text: '↺',
            attrs: {
              title: 'Undo'
            },
            parent: perActions,
            click: (e) => {
              e.preventDefault();
              e.stopPropagation();
              undoFileChange(f.path);
            }
          });
        });
      };

      const renderManifest = () => {
        // [TRANSITION] Shadow UI Bridge
        if (Deck0.ui.shadow?.enabled && Deck0.ui.shadow.renderTaskList) {
          Deck0.ui.shadow.renderTaskList();
        }
        const list = Deck0.ui.refs.tasksBody;
        if (!list) return;
        list.replaceChildren();



        // Auto-Expand if tasks exist
        if (store.getState().protocol.plans.length > 0) {
          const tasksBody = Deck0.ui.refs.tasksBody;
          if (tasksBody) {
            tasksBody.classList.add('open');
            const acc = tasksBody.closest(DECK0_SELECTORS.UI_ACCORDION);
            if (acc) {
              const arrow = acc.querySelector(DECK0_SELECTORS.UI_ACC_ARROW);
              if (arrow) arrow.style.transform = 'rotate(0deg)';
            }
          }
        }

        // Tasks progress header (Cline-like)
        tasksProgressUI.root = null;
        tasksProgressUI.badge = null;
        tasksProgressUI.text = null;
        tasksProgressUI.count = null;
        tasksProgressUI.spinner = null;
        tasksProgressUI.check = null;
        tasksProgressUI.bar = null;

        const progressCard = el('div', {
          cls: 'deck0-tasks-progress',
          parent: list
        });
        tasksProgressUI.root = progressCard;
        const progressRow = el('div', {
          cls: 'deck0-tasks-progress-row',
          parent: progressCard
        });
        const progressLeft = el('div', {
          cls: 'deck0-tasks-progress-left',
          parent: progressRow
        });
        tasksProgressUI.badge = el('span', {
          cls: 'deck0-tasks-progress-badge',
          text: '0/0',
          parent: progressLeft
        });
        tasksProgressUI.text = el('div', {
          cls: 'deck0-tasks-progress-text',
          text: 'Waiting for tasks…',
          parent: progressLeft
        });

        const progressRight = el('div', {
          cls: 'deck0-tasks-progress-right',
          parent: progressRow
        });
        tasksProgressUI.count = el('span', {
          cls: 'deck0-tasks-progress-count',
          text: '0/0',
          parent: progressRight
        });
        tasksProgressUI.spinner = el('div', {
          cls: 'deck0-spinner',
          parent: progressRight,
          style: {
            display: 'none'
          }
        });
        tasksProgressUI.check = el('span', {
          cls: 'deck0-tasks-progress-check',
          text: '✔',
          parent: progressRight,
          style: {
            display: 'none'
          }
        });
        tasksProgressUI.bar = el('div', {
          cls: 'deck0-tasks-progress-underline',
          parent: progressCard
        });

        store.getState().protocol.plans.forEach(plan => {
          const row = el('div', {
            cls: 'deck0-task-row',
            id: `plan-${plan.id}`,
            parent: list,
            mouseenter: (e) => showPlanDetails(plan.id, e.currentTarget),
            mouseleave: startHideFlyoutTimer
          });

          // Status Dot
          el('div', {
            cls: 'deck0-task-status-dot ' + plan.status,
            id: `status-${plan.id}`,
            parent: row
          });

          // ID
          const prefix = plan.id.split('-')[0];
          const tagColor = (TAG_COLORS[prefix] || {}).text || '#888';
          el('div', {
            cls: 'deck0-task-id',
            text: plan.id,
            style: {
              color: tagColor
            },
            parent: row
          });

          // Title
          const titleEl = el('div', {
            cls: 'deck0-task-title',
            parent: row
          });
          const titleMatch = plan.title.match(/^([a-z]+)(\(.*\))?:(.*)$/i);
          if (titleMatch) {
            const type = titleMatch[1].toLowerCase();
            const rest = titleMatch[3].trim();
            const typeColor = {
              feat: THEME.success,
              fix: THEME.danger,
              refactor: THEME.accent,
              chore: THEME.grey
            }[type] || THEME.textMain;
            el('span', {
              text: `${type}:`,
              style: {
                color: typeColor,
                fontWeight: 'bold',
                marginRight: '4px'
              },
              parent: titleEl
            });
            titleEl.appendChild(document.createTextNode(rest));
          } else {
            titleEl.textContent = plan.title;
          }

          // Actions (Hover)
          const actions = el('div', {
            cls: 'deck0-task-actions',
            parent: row
          });

          el('div', {
            cls: 'deck0-btn-mini run',
            text: '▶',
            attrs: {
              title: 'Execute'
            },
            parent: actions,
            click: (e) => {
              e.stopPropagation();
              executeSinglePlan(plan.id);
            }
          });

          el('div', {
            cls: 'deck0-btn-mini undo',
            text: '↺',
            attrs: {
              title: 'Undo'
            },
            parent: actions,
            click: (e) => {
              e.stopPropagation();
              undoSinglePlan(plan.id);
            }
          });

          // Marker Container (Hidden by default)
          el('div', {
            cls: 'deck0-plan-marker',
            parent: row
          });
        });

        // Initial render of changed files (likely empty)
        renderChangedFiles();
        updateTasksProgressUI();
      };

      const updateTasksProgressUI = () => {
        const total = Array.isArray(store.getState().protocol.plans) ? store.getState().protocol.plans.length : 0;
        const done = total > 0 ? store.getState().protocol.plans.filter(p => p.status === 'done').length : 0;
        const firstIncompleteIndex = total > 0 ? store.getState().protocol.plans.findIndex(p => p.status !== 'done') : -1;
        const currentIndex = firstIncompleteIndex >= 0 ? firstIncompleteIndex + 1 : total;
        const currentPlan = firstIncompleteIndex >= 0 ? store.getState().protocol.plans[firstIncompleteIndex] : null;
        const currentText = currentPlan ? (currentPlan.title || currentPlan.id || 'In progress…') : (total > 0 ? 'All tasks have been completed!' : 'No tasks detected.');
        const percent = total > 0 ? Math.round((done / total) * 100) : 0;
        const hasRunning = total > 0 ? store.getState().protocol.plans.some(p => p.status === 'running') : false;
        const isCompleted = total > 0 && done === total;

        if (tasksCount) tasksCount.textContent = total > 0 ? `TASKS (${done}/${total})` : 'TASKS (0)';

        if (!tasksProgressUI.root) return;
        if (tasksProgressUI.badge) tasksProgressUI.badge.textContent = total > 0 ? `${currentIndex}/${total}` : '0/0';
        if (tasksProgressUI.text) tasksProgressUI.text.textContent = currentText;
        if (tasksProgressUI.count) tasksProgressUI.count.textContent = total > 0 ? `${done}/${total}` : '0/0';
        if (tasksProgressUI.spinner) tasksProgressUI.spinner.style.display = hasRunning ? 'block' : 'none';
        if (tasksProgressUI.check) tasksProgressUI.check.style.display = (!hasRunning && isCompleted) ? 'block' : 'none';
        if (tasksProgressUI.bar) {
          tasksProgressUI.bar.style.width = `${percent}%`;
          tasksProgressUI.bar.style.opacity = (percent === 0 || percent === 100) ? '0' : '0.9';
        }

        if (tasksProgressUI.badge) {
          if (isCompleted) {
            tasksProgressUI.badge.style.background = 'rgba(48, 209, 88, 0.25)';
            tasksProgressUI.badge.style.color = '#30d158';
          } else {
            tasksProgressUI.badge.style.background = 'rgba(255,255,255,0.1)';
            tasksProgressUI.badge.style.color = '#c5c5c5';
          }
        }
      };
      // [V16.8] Execution Marker Helper
      const setPlanMarker = (planId, type) => {
        const card = Deck0.ui.refs.getById(`plan-${planId}`);
        if (!card) return;

        let marker = card.querySelector(DECK0_SELECTORS.UI_PLAN_MARKER);
        if (!marker) {
          marker = el('div', {
            cls: 'deck0-plan-marker',
            parent: card
          });
        }

        marker.replaceChildren(); // Clear previous icon

        if (type === 'success') {
          // Green Check SVG
          el('svg', {
            attrs: {
              viewBox: '0 0 24 24',
              width: '16',
              height: '16',
              fill: '#30d158'
            },
            parent: marker,
            children: [
              el('path', {
                attrs: {
                  d: 'M12,24C5.4,24,0,18.6,0,12S5.4,0,12,0s12,5.4,12,12S18.6,24,12,24z M12,2C6.5,2,2,6.5,2,12s4.5,10,10,10s10-4.5,10-10 S17.5,2,12,2z'
                }
              }),
              el('path', {
                attrs: {
                  d: 'M11,16c-0.3,0-0.5-0.1-0.7-0.3l-3-3c-0.4-0.4-0.4-1,0-1.4s1-0.4,1.4,0l3,3c0.4,0.4,0.4,1,0,1.4C11.5,15.9,11.3,16,11,16z'
                }
              }),
              el('path', {
                attrs: {
                  d: 'M11,16c-0.3,0-0.5-0.1-0.7-0.3c-0.4-0.4-0.4-1,0-1.4l6-6c0.4-0.4,1-0.4,1.4,0s0.4,1,0,1.4l-6,6C11.5,15.9,11.3,16,11,16z'
                }
              })
            ]
          });
          marker.classList.add('visible');
        } else if (type === 'reverted') {
          // Red Failure/Revert SVG
          el('svg', {
            attrs: {
              viewBox: '0 0 24 24',
              width: '16',
              height: '16',
              fill: '#ff453a'
            },
            parent: marker,
            children: [
              el('path', {
                attrs: {
                  d: 'M12,24C5.4,24,0,18.6,0,12S5.4,0,12,0s12,5.4,12,12S18.6,24,12,24z M12,2C6.5,2,2,6.5,2,12s4.5,10,10,10s10-4.5,10-10 S17.5,2,12,2z'
                }
              }),
              el('path', {
                attrs: {
                  d: 'M19.8,20.8c-0.3,0-0.5-0.1-0.7-0.3L3.5,4.9c-0.4-0.4-0.4-1,0-1.4s1-0.4,1.4,0l15.6,15.6c0.4,0.4,0.4,1,0,1.4 C20.3,20.7,20,20.8,19.8,20.8z'
                }
              })
            ]
          });
          marker.classList.add('visible');
        } else {
          marker.classList.remove('visible');
        }
      };

      const updatePlanStatus = (id, status) => {
        const plan = store.getState().protocol.plans.find(p => p.id === id);
        if (!plan) return;
        if (plan.status === 'done' && status === 'running') return;
        if (plan.status !== status) {
          plan.status = status;
          const ind = Deck0.ui.refs.getById(`status-${id}`);
          if (ind) ind.className = `deck0-task-status-dot ${status}`;

          // [UX FIX]: Changed log type to 'warn' so the text becomes yellow/amber
          if (status === 'running') log(`Starting: ${id}`, 'warn', {
            text: '▶',
            color: THEME.warning
          });

          if (status === 'done') {
            log(`Completed: ${id}`, 'success', {
              text: '✔',
              color: THEME.success
            });
            completedPlansCounter++;
            // [UX FIX]: Stop breathing animation and set to solid green when ALL plans are done
            if (completedPlansCounter === totalPlansInManifest) {
              const badge = Deck0.ui.refs.statusBadge;
              if (badge) {
                badge.classList.remove('deck0-badge-thinking');
                badge.classList.remove('deck0-badge-generating');
                setBadgeState('READY', THEME.success); // Solid Green
              }

              // [V16.6] Audio Alert
              if (Deck0.store.getState().settings.audioUrl) {
                try {
                  if (completionAudio) completionAudio.pause();
                  completionAudio = new Audio(Deck0.store.getState().settings.audioUrl);
                  completionAudio.volume = 0.5;
                  completionAudio.play().catch(e => console.error("Audio Play failed", e));

                  const stopAudio = () => {
                    if (completionAudio) {
                      completionAudio.pause();
                      completionAudio = null;
                    }
                    document.removeEventListener('click', stopAudio);
                  };
                  document.addEventListener('click', stopAudio);
                } catch (e) {
                  log("Audio Error: " + e.message, 'warn');
                }
              }

              // [V16.9] UX: Auto-switch back to logs when done
              // switchTermTab('logs'); // [MOD] Disabled auto-switch

              if (updateSendBtnState) updateSendBtnState(false);
              Deck0Effects.clock.setTimeout(() => log("🎉 AI has finished planning. The execution plan is ready.", 'purple'), 0);
            }
          }
          updateTasksProgressUI();
        }
      };
      const mountProject = async () => Orchestrator.mountProject();

      const ensureVirtualMount = () => {
        if ((Deck0.store.getState().project.storageMode === 'VIRTUAL' || Deck0.store.getState().project.storageMode === 'LINK') && !Deck0.store.getState().project.dirHandle) {
          Deck0.store.mutate('project', p => {
            p.dirHandle = {
              name: 'Virtual_Drive',
              kind: 'directory'
            };
          });
        }
      };

      const refreshFileTree = async (silent = false) => {
        const ignoreList = Deck0.store.getState().project.ignoreList;
        const fileSelectionMap = Deck0.store.getState().ui.fileSelectionMap;
        ensureVirtualMount();
        if (!Deck0.store.getState().project.dirHandle) return;

        // [V17.1 Fix] Flicker Prevention (Link/Virtual)
        if (silent && (Deck0.store.getState().project.storageMode === 'VIRTUAL' || Deck0.store.getState().project.storageMode === 'LINK')) {
          try {
            if (Deck0.ui.shadow?.enabled && Deck0.ui.shadow.renderTaskList) {
              Deck0.ui.shadow.renderTaskList();
            }
            let checkKeys = [];
            if (Deck0.store.getState().project.storageMode === 'VIRTUAL') checkKeys = await IDBHelper.getAllKeys();
            else checkKeys = await LinkAdapter.getTree();

            const checkStr = JSON.stringify(checkKeys.sort());
            // [V18.14 FIX] Force redraw if View Mode (Diff/TimeTravel) changed
            const currentViewModeKey = Deck0.store.getState().legacy.DECK0_VIEW ? (Deck0.store.getState().legacy.DECK0_VIEW.mode + ':' + (Deck0.store.getState().legacy.DECK0_VIEW.snapshotId || '')) : 'normal';

            if (checkStr === lastTreeKeysStr && currentViewModeKey === lastViewModeKey) return; // No change, skip rebuild

            lastTreeKeysStr = checkStr;
            lastViewModeKey = currentViewModeKey;
          } catch (e) {
            console.warn('Tree check failed', e);
          }
        }

        // Save scroll if silent update
        const prevScroll = silent ? fileTree.scrollTop : 0;

        fileTree.textContent = '';
        fileSelectionMap.clear();
        let folderSelectionMap = new Map();
        const ignore = ignoreList.split(',').map(s => s.trim()).filter(s => s);

        // [V16.6] Persistence Logic
        const projectKey = Deck0.store.getState().project.dirHandle.name || 'default';
        const excludedFiles = Deck0.store.getState().project.fileSelections[projectKey] || [];

        const updatePersistence = () => {
          const excluded = [];
          fileSelectionMap.forEach((chk, path) => {
            if (!chk.checked) excluded.push(path);
          });
          Deck0Actions.project.updateFileSelections(projectKey, excluded);
          saveSettings();
        };

        const attachItemEvents = (item, path, kind) => {
          item.addEventListener('contextmenu', (e) => {
            e.preventDefault();
            e.stopPropagation();
            showContextMenu(e, path, kind, item);
          });

          // [V17.0 UI] Folder Checkbox Logic
          if (kind === 'directory') {
            // Persistence: If any child is excluded, folder is unchecked
            const isChecked = !excludedFiles.some(p => p.startsWith(path + '/'));

            const chk = el('input', {
              cls: 'deck0-tree-checkbox',
              attrs: {
                type: 'checkbox'
              },
              style: {
                marginRight: '6px'
              },
              parent: item
            });
            chk.checked = isChecked;

            folderSelectionMap.set(path, chk);

            const propagate = (state) => {
              chk.checked = state;
              fileSelectionMap.forEach((c, p) => {
                if (p.startsWith(path + '/')) c.checked = state;
              });
              folderSelectionMap.forEach((c, p) => {
                if (p.startsWith(path + '/')) c.checked = state;
              });
              updatePersistence();
            };

            chk.addEventListener('click', (e) => {
              e.stopPropagation();
              propagate(chk.checked);
            });

            // Drag Support
            chk.addEventListener('mousedown', (e) => {
              e.stopPropagation();
              const newState = !chk.checked;
              isDragSelecting = true;
              dragSelectionState = newState;
              propagate(newState);
            });

            chk.addEventListener('mouseenter', () => {
              if (isDragSelecting && chk.checked !== dragSelectionState) {
                propagate(dragSelectionState);
              }
            });
          }

          if (kind === 'file') {
            // Checkbox
            const isChecked = !excludedFiles.includes(path);
            const chk = el('input', {
              cls: 'deck0-tree-checkbox',
              attrs: {
                type: 'checkbox'
              },
              parent: item
            });
            chk.checked = isChecked;

            fileSelectionMap.set(path, chk);

            const toggle = (newState) => {
              chk.checked = newState;
              updatePersistence();
            };

            chk.addEventListener('mousedown', (e) => {
              e.stopPropagation();
              const newState = !chk.checked;
              toggle(newState);
              isDragSelecting = true;
              dragSelectionState = newState;
            });
            chk.addEventListener('click', (e) => {
              e.stopPropagation();
              e.preventDefault();
            });
            chk.addEventListener('mouseenter', () => {
              if (isDragSelecting && chk.checked !== dragSelectionState) {
                toggle(dragSelectionState);
              }
            });

            const label = el('span', {
              text: path.split('/').pop(),
              style: {
                marginLeft: '4px',
                flex: 1
              },
              parent: item
            });
            item.addEventListener('click', (e) => {
              if (e.target !== chk) {
                if (typeof EditorManager !== 'undefined') EditorManager.open(path);
              }
            });
            // [MOD] Zen Mode Removed
          } else {
            // Directory Label & Folding
            const label = el('span', {
              text: `📂 ${path.split('/').pop()}`,
              style: {
                fontWeight: 'bold',
                color: '#eee',
                cursor: 'pointer',
                userSelect: 'none'
              },
              parent: item
            });

            item.classList.add('deck0-folder-item');
            item.dataset.path = path;
          }
        };

        if (Deck0.store.getState().project.storageMode === 'VIRTUAL' || Deck0.store.getState().project.storageMode === 'LINK') {
          // === VIRTUAL & LINK RENDERER ===
          let keys = [];
          if (Deck0.store.getState().project.storageMode === 'VIRTUAL') keys = await IDBHelper.getAllKeys();
          else keys = await LinkAdapter.getTree();

          // Build Tree Object
          const tree = {};
          keys.forEach(path => {
            const parts = path.split('/');
            // [FIX] Use segment matching instead of substring to allow 'build.ts' vs 'build'
            const shouldIgnore = ignore.some(ignoredItem => parts.includes(ignoredItem));
            // [FIX] Allow .gitkeep (contains .git) to ensure folders are visible
            if (shouldIgnore && !path.endsWith('.gitkeep')) return;

            let cur = tree;
            parts.forEach((part, i) => {
              if (!cur[part]) cur[part] = (i === parts.length - 1) ? {
                __kind: 'file',
                __path: path
              } : {
                __kind: 'dir',
                __children: {}
              };
              if (cur[part].__kind === 'dir') cur = cur[part].__children;
            });
          });

          // [V18.10 UI] Diff Focus Mode Helpers
          const isDiffMode = Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.statusMap;
          const getDiffStatus = (path) => {
            if (!isDiffMode) return null;
            // [V18.13 FIX] Robust Path Normalization for Status Lookup
            const clean = (path || '').trim().replace(/\\/g, '/');
            const s = Deck0.store.getState().legacy.DECK0_VIEW.statusMap.get(clean);
            return (!s || s === 'unchanged') ? null : s;
          };

          const renderVirtual = (node, container, prefix = '') => {
            let hasActiveChildren = false;
            let hasDiffChildren = false;

            const entries = Object.entries(node).sort((a, b) => {
              const aKind = a[1].__kind, bKind = b[1].__kind;
              if (aKind === bKind) return a[0].localeCompare(b[0]);
              return aKind === 'dir' ? -1 : 1;
            });

            entries.forEach(([name, data]) => {
              const kind = data.__kind === 'dir' ? 'directory' : 'file';
              const path = prefix ? `${prefix}/${name}` : name;
              const item = el('div', {
                cls: 'deck0-tree-item',
                parent: container,
                attrs: { 'data-path': path, 'data-kind': kind }
              });
              attachItemEvents(item, path, kind);

              if (kind === 'directory') {
                const subContainer = el('div', {
                  style: { paddingLeft: '14px', borderLeft: '1px solid rgba(255,255,255,0.05)', display: 'block' },
                  parent: container
                });

                item.addEventListener('click', (e) => {
                  if (e.target.type === 'checkbox') return;
                  e.stopPropagation();
                  const isClosed = subContainer.style.display === 'none';
                  subContainer.style.display = isClosed ? 'block' : 'none';
                  // Only toggle opacity if NOT in diff mode (in diff mode, opacity is strict)
                  if (!isDiffMode) item.style.opacity = isClosed ? '1' : '0.7';
                });

                const childResult = renderVirtual(data.__children, subContainer, path);
                if (childResult.active) hasActiveChildren = true;
                if (childResult.diff) hasDiffChildren = true;

                if (childResult.active) {
                  subContainer.style.display = 'block';
                  item.style.opacity = '1';
                } else {
                  subContainer.style.display = 'none';
                  item.style.opacity = '0.7';
                }

                // [Diff Mode] Folder Opacity
                if (isDiffMode) {
                  item.style.opacity = childResult.diff ? '1' : '0.3';
                }

              } else {
                if (!excludedFiles.includes(path)) hasActiveChildren = true;

                // [Diff Mode] File Styling
                if (isDiffMode) {
                  const status = getDiffStatus(path);
                  if (status) {
                    hasDiffChildren = true;
                    item.style.opacity = '1';
                    // [V18.13 FIX] Ensure label shrinks to fit marker
                    const label = item.querySelector(DECK0_SELECTORS.UI_TREE_LABEL_SPAN_FLEX);
                    if (label) { label.style.minWidth = '0'; label.style.overflow = 'hidden'; label.style.textOverflow = 'ellipsis'; }

                    const symbol = status === 'added' ? '+' : (status === 'deleted' ? '-' : '~');
                    const color = status === 'added' ? THEME.success : (status === 'deleted' ? THEME.danger : THEME.warning);
                    el('span', {
                      text: symbol,
                      style: { marginLeft: 'auto', color: color, fontWeight: 'bold', fontSize: '11px', minWidth: '16px', textAlign: 'center', flexShrink: '0' },
                      parent: item
                    });
                  } else {
                    item.style.opacity = '0.3';
                  }
                }
              }
            });
            return { active: hasActiveChildren, diff: hasDiffChildren };
          };
          renderVirtual(tree, fileTree);

        } else {
          // === LOCAL RENDERER ===
          const isDiffMode = Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.statusMap;
          const getDiffStatus = (path) => {
            if (!isDiffMode) return null;
            const clean = normalizeVfsPath(path).replace(/\\/g, '/');
            const s = Deck0.store.getState().legacy.DECK0_VIEW.statusMap.get(clean);
            return (!s || s === 'unchanged') ? null : s;
          };

          const renderTree = async (dir, container, prefix = '') => {
            let hasActiveChildren = false;
            let hasDiffChildren = false;

            const entries = [];
            for await (const entry of dir.values()) entries.push(entry);
            entries.sort((a, b) => {
              if (a.kind === b.kind) return a.name.localeCompare(b.name);
              return a.kind === 'directory' ? -1 : 1;
            });

            for (const entry of entries) {
              const path = prefix ? `${prefix}/${entry.name}` : entry.name;
              const pathParts = path.split('/');
              const shouldIgnore = ignore.some(ignoredItem => pathParts.includes(ignoredItem));
              if (shouldIgnore && !path.endsWith('.gitkeep')) continue;

              const item = el('div', {
                cls: 'deck0-tree-item',
                parent: container,
                attrs: { 'data-path': path, 'data-kind': entry.kind }
              });
              attachItemEvents(item, path, entry.kind);

              if (entry.kind === 'directory') {
                const subContainer = el('div', {
                  style: { paddingLeft: '14px', borderLeft: '1px solid rgba(255,255,255,0.05)', display: 'block' },
                  parent: container
                });

                item.addEventListener('click', (e) => {
                  if (e.target.type === 'checkbox') return;
                  e.stopPropagation();
                  const isClosed = subContainer.style.display === 'none';
                  subContainer.style.display = isClosed ? 'block' : 'none';
                  if (!isDiffMode) item.style.opacity = isClosed ? '1' : '0.7';
                });

                const childResult = await renderTree(entry, subContainer, path);
                if (childResult.active) hasActiveChildren = true;
                if (childResult.diff) hasDiffChildren = true;

                if (childResult.active) {
                  subContainer.style.display = 'block';
                  item.style.opacity = '1';
                } else {
                  subContainer.style.display = 'none';
                  item.style.opacity = '0.7';
                }

                // [Diff Mode] Folder Opacity
                if (isDiffMode) {
                  item.style.opacity = childResult.diff ? '1' : '0.3';
                }

              } else {
                if (!excludedFiles.includes(path)) hasActiveChildren = true;

                // [Diff Mode] File Styling
                if (isDiffMode) {
                  const status = getDiffStatus(path);
                  if (status) {
                    hasDiffChildren = true;
                    item.style.opacity = '1';
                    // [V18.13 FIX] Ensure label shrinks to fit marker
                    const label = item.querySelector(DECK0_SELECTORS.UI_TREE_LABEL_SPAN_FLEX);
                    if (label) { label.style.minWidth = '0'; label.style.overflow = 'hidden'; label.style.textOverflow = 'ellipsis'; }

                    const symbol = status === 'added' ? '+' : (status === 'deleted' ? '-' : '~');
                    const color = status === 'added' ? THEME.success : (status === 'deleted' ? THEME.danger : THEME.warning);
                    el('span', {
                      text: symbol,
                      style: { marginLeft: 'auto', color: color, fontWeight: 'bold', fontSize: '11px', minWidth: '16px', textAlign: 'center', flexShrink: '0' },
                      parent: item
                    });
                  } else {
                    item.style.opacity = '0.3';
                  }
                }
              }
            }
            return { active: hasActiveChildren, diff: hasDiffChildren };
          };
          await renderTree(Deck0.store.getState().project.dirHandle, fileTree);
        }

        fileTree.oncontextmenu = (e) => {
          if (e.target.closest(DECK0_SELECTORS.UI_TREE_ITEM)) return;
          e.preventDefault();
          e.stopPropagation();
          showContextMenu(e, '', 'root', fileTree);
        };

        // [V18.0 UX] Restore scroll or Auto-scroll
        if (silent) {
          if (fileTree.scrollTop === 0 && prevScroll > 0) fileTree.scrollTop = prevScroll;
        } else {
          Deck0Effects.clock.setTimeout(() => {
            const firstChecked = fileTree.querySelector(DECK0_SELECTORS.UI_CHECKBOX_CHECKED);
            if (firstChecked) {
              firstChecked.scrollIntoView({
                block: 'center',
                behavior: 'smooth'
              });
            }

            // [V18.4] Auto-open first checked file if Editor is empty
            if (!EditorManager.active) {
              const firstFile = fileTree.querySelector(DECK0_SELECTORS.UI_FILE_CHECKBOX_CHECKED);
              if (firstFile) {
                const item = firstFile.closest(DECK0_SELECTORS.UI_TREE_ITEM);
                if (item && item.dataset.path) EditorManager.open(item.dataset.path);
              }
            }
          }, 50);
        }
        if (typeof EditorManager !== 'undefined' && EditorManager.active) {
          highlightExplorerFile(EditorManager.active);
        }
      };

      // ================= ZEN MODE EDITOR (REMOVED) =================

      // ================= CONTEXT MENU =================
      const showContextMenu = (e, path, kind, domElement) => {
        if (!ctxMenuElement) {
          ctxMenuElement = el('div', {
            cls: 'deck0-ctx-menu',
            parent: document.body
          });
          document.addEventListener('click', () => {
            ctxMenuElement.style.display = 'none';
          });
          document.addEventListener('contextmenu', (evt) => {
            if (!evt.target.closest('.deck0-tree-item')) ctxMenuElement.style.display = 'none';
          });
        }

        ctxMenuElement.replaceChildren();

        // Actions
        const addAction = (label, icon, shortcut, fn) => {
          const item = el('div', {
            cls: 'deck0-ctx-item',
            parent: ctxMenuElement,
            click: (ev) => {
              ev.stopPropagation();
              ctxMenuElement.style.display = 'none';
              fn();
            }
          });
          el('span', {
            text: icon,
            style: {
              width: '16px',
              textAlign: 'center'
            },
            parent: item
          });
          el('span', {
            text: label,
            parent: item
          });
          if (shortcut) el('span', {
            cls: 'deck0-ctx-shortcut',
            text: shortcut,
            parent: item
          });
        };

        // 1. Creation Ops (Universal)
        let creationPath = path;
        if (kind === 'file') {
          // If clicking a file, create sibling (use parent dir)
          const parts = path.split('/');
          parts.pop();
          creationPath = parts.join('/');
        }

        addAction('New File', '📄', '', () => triggerInlineInput(domElement, creationPath, 'new-file'));
        addAction('New Folder', '📂', '', () => triggerInlineInput(domElement, creationPath, 'new-folder'));
        el('div', {
          cls: 'deck0-ctx-sep',
          parent: ctxMenuElement
        });

        // 2. File Ops (Non-Root)
        if (kind !== 'root') {
          addAction('Rename', '✏️', 'F2', () => triggerInlineInput(domElement, path, 'rename'));
        }

        addAction('Copy Path', '📋', '', () => {
          const p = kind === 'root' ? (Deck0.store.getState().project.dirHandle?.name || 'root') : path;
          navigator.clipboard.writeText(p);
          log(`Path copied: ${p}`, 'info');
        });

        // Reveal in OS (Link Mode)
        if (Deck0.store.getState().project.storageMode === 'LINK' && kind !== 'root') {
          addAction('Reveal in OS', '📂', '', async () => {
            try {
              await LinkAdapter.reveal(path);
              ctxMenuElement.style.display = 'none';
            } catch (e) {
              alert(e.message);
            }
          });
        }

        if (kind !== 'root') {
          el('div', {
            cls: 'deck0-ctx-sep',
            parent: ctxMenuElement
          });

          const isDir = kind === 'directory';
          const deleteLabel = isDir ? 'Delete Folder' : 'Delete';

          addAction(deleteLabel, '🗑️', 'Del', async () => {
            const msg = isDir ?
              `⚠️ DANGER: Recursively delete folder "${path}" and ALL contents?\n\nThis cannot be undone.` :
              `Permanently delete ${path}?`;

            if (confirm(msg)) {
              // Logic to handle directory deletion in VFS
              if (Deck0.store.getState().project.storageMode === 'VIRTUAL' && isDir) {
                const keys = await IDBHelper.getAllKeys();
                const toDelete = keys.filter(k => k === path || k.startsWith(path + '/'));
                let count = 0;
                for (const k of toDelete) {
                  await IDBHelper.delete(k);
                  count++;
                }
                log(`Deleted folder ${path} (${count} items)`, 'success');
                refreshFileTree();
              } else {
                // LOCAL and LINK handle recursive via their adapters/APIs
                // [TP-P3-4] Use Executor.delete (includes snapshot/syncList/UI sync)
                if (await Executor.delete(path)) {
                  log(`Deleted ${path}`, 'success');
                  refreshFileTree();
                }
              }
            }
          });
        }

        // Position (Smart Overflow Protection)
        ctxMenuElement.style.visibility = 'hidden'; // Hide to measure
        ctxMenuElement.style.display = 'flex';

        const rect = ctxMenuElement.getBoundingClientRect();
        const winWidth = window.innerWidth;
        const winHeight = window.innerHeight;

        let finalX = e.clientX;
        let finalY = e.clientY;

        // Vertical Collision (Bottom Edge)
        if (finalY + rect.height > winHeight) {
          finalY = finalY - rect.height;
        }

        // Horizontal Collision (Right Edge)
        if (finalX + rect.width > winWidth) {
          finalX = finalX - rect.width;
        }

        ctxMenuElement.style.left = `${finalX}px`;
        ctxMenuElement.style.top = `${finalY}px`;
        ctxMenuElement.style.visibility = 'visible';
      };

      const triggerInlineInput = (targetEl, basePath, action) => {
        let parentEl = targetEl;
        let originalText = '';
        let isNew = false;
        let isCommitting = false;

        // If creating new inside a folder, append input to folder's subcontainer
        if (action === 'new-file' || action === 'new-folder') {
          isNew = true;
        }

        const input = el('input', {
          cls: 'deck0-inline-input',
          attrs: {
            type: 'text'
          }
        });

        const commit = async () => {
          if (isCommitting) return;
          isCommitting = true;

          const val = input.value.trim();
          if (!val) {
            isCommitting = false;
            cancel();
            return;
          }

          try {
            if (action === 'rename') {
              if (val === originalText) {
                cancel();
                return;
              }
              const oldPath = basePath;
              const parts = oldPath.split('/');
              parts.pop();
              const newPath = parts.length ? parts.join('/') + '/' + val : val;

              // Universal File Rename (Read -> Write -> Delete)
              try {
                const content = await Deck0.vfs.read(oldPath);
                // [TP-P3-4] Use Executor.write/delete (includes snapshot/syncList/UI sync)
                await Executor.write(newPath, content);
                await Executor.delete(oldPath);
                log(`Renamed to ${newPath}`, 'success');
                refreshFileTree();
              } catch (e) {
                console.error(e);
                alert('Rename failed. Note: Folder renaming is currently limited.');
              }

            } else if (action === 'new-file') {
              const newPath = basePath ? `${basePath}/${val}` : val;
              // [TP-P3-4] Use Executor.write (includes snapshot/syncList/UI sync)
              await Executor.write(newPath, '');
              log(`Created ${newPath}`, 'success');

              await EditorManager.open(newPath);
              scheduleUiSync('new-file', { forceEditor: true });

            } else if (action === 'new-folder') {
              const newPath = basePath ? `${basePath}/${val}` : val;
              if (Deck0.store.getState().project.storageMode === 'LOCAL') {
                await getDir(Deck0.store.getState().project.dirHandle, newPath, true);
              } else {
                // VFS/LINK: Create .gitkeep to persist folder
                // [TP-P3-4] Use Executor.write (includes snapshot/syncList/UI sync)
                await Executor.write(newPath + '/.gitkeep', '');
              }
              log(`Created Folder ${newPath}`, 'success');
              refreshFileTree();
            }
          } catch (e) {
            alert(e.message);
            isCommitting = false;
            cancel();
          }
        };

        const cancel = () => {
          if (action === 'rename') {
            targetEl.replaceChildren(...originalChildren);
          } else {
            input.parentElement.remove();
          }
        };

        input.addEventListener('keydown', (e) => {
          if (e.key === 'Enter') commit();
          if (e.key === 'Escape') cancel();
        });

        input.addEventListener('blur', () => {
          // Optional: Auto-commit on blur? Or cancel? VSCode commits.
          commit();
        });

        if (action === 'rename') {
          originalText = basePath.split('/').pop();
          // Save original children to restore if cancelled
          var originalChildren = Array.from(targetEl.childNodes);
          targetEl.textContent = '';
          targetEl.appendChild(input);
          input.value = originalText;
          input.select();
        } else {
          // New File/Folder
          // Determine visual nesting depth based on target
          let paddingLeft = '10px';
          if (targetEl.style.paddingLeft) {
            paddingLeft = (parseInt(targetEl.style.paddingLeft) + 14) + 'px';
          } else if (targetEl.classList.contains('deck0-list')) {
            paddingLeft = '4px'; // Root level
          }

          const container = el('div', {
            cls: 'deck0-tree-item',
            style: {
              paddingLeft: paddingLeft
            }
          });
          el('span', {
            text: action === 'new-folder' ? '📂 ' : '📄 ',
            parent: container
          });
          container.appendChild(input);

          // DOM Insertion Logic
          if (targetEl.classList.contains('deck0-list')) {
            // CASE: Root (Empty Space) - Append to end of list
            targetEl.appendChild(container);
          } else if (targetEl.dataset.kind === 'directory') {
            // CASE: Inside Folder - Prepend to sub-container (Next Sibling)
            const subContainer = targetEl.nextSibling;
            if (subContainer) {
              if (subContainer.firstChild) subContainer.insertBefore(container, subContainer.firstChild);
              else subContainer.appendChild(container);
            } else {
              // Fallback (Shouldn't happen given renderTree structure)
              targetEl.parentNode.appendChild(container);
            }
          } else {
            // CASE: Sibling (fallback)
            if (targetEl.nextSibling) targetEl.parentNode.insertBefore(container, targetEl.nextSibling);
            else targetEl.parentNode.appendChild(container);
          }

          input.focus();
        }
      };

      // [DECK0 UI Coordinator] Unified Sync Logic (Debounced)

      // [V18.9] Helper: One-shot auto open with locking
      const autoOpenFirstDiffIfAny = async () => {
        try {
          if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode) return false;
          const view = Deck0.store.getState().legacy.DECK0_VIEW;
          if (view && view.mode) return false;
          if (!Deck0.store.getState().runtime || !Deck0.store.getState().runtime.generationDone) return false;
          // Lock Check
          if (Deck0.store.getState().runtime.autoDiffOpenedRunId === Deck0.store.getState().runtime.runId) return false;

          const focus = _firstDiffablePath ? normalizeVfsPath(_firstDiffablePath) : null;
          const ok = await enterDiffReviewMode(focus, { silent: true });
          if (ok) {
            Deck0.actions.runtime.setAutoDiffOpenedRunId(Deck0.store.getState().runtime.runId);
            log('🔎 Auto-opened Diff Review', 'purple');
            return true;
          }
        } catch (e) {
          log(`Auto-diff failed: ${e.message}`, 'warn');
        }
        return false;
      };

      let uiSyncTimer = null;
      const scheduleUiSync = (reason, options = {}) => {
        if (uiSyncTimer) Deck0Effects.clock.clearTimeout(uiSyncTimer);
        uiSyncTimer = Deck0Effects.clock.setTimeout(async () => {
          // 1. Force Tree Refresh
          await refreshFileTree(true);

          // 2. Sync Editor Tabs (Close deleted files immediately)
          if (options.forceEditor && typeof EditorManager !== 'undefined') {
            const filesToCheck = [...EditorManager.files];
            for (const f of filesToCheck) {
              if (!f || !f.path) continue;
              try {
                // In LINK mode, this throws if file is 404
                await readFileContent(f.path);
              } catch (e) {
                console.log(`[Sync] Closing deleted tab: ${f.path}`);
                EditorManager.close(f.path);
              }
            }
            // Refresh content of remaining open files
            await EditorManager.refreshOpenFiles();
          }

          // 3. Refresh Changed Files List
          await renderChangedFiles();

          // 4. Auto-Diff Trigger (generation-complete)
          // [GPT-5.2 Fix] No polling, one-shot delayed trigger
          if (reason === 'generation-complete') {
            if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode) return;
            if (Deck0.store.getState().runtime.autoDiffOpenedRunId === Deck0.store.getState().runtime.runId) return;

            // Defer one tick to ensure renderChangedFiles has updated _firstDiffablePath
            Deck0Effects.clock.setTimeout(async () => {
              await autoOpenFirstDiffIfAny();
            }, 120);
          }
        }, 100);
      };

      const getDir = async (root, path, create = false) => {
        const parts = path.split('/').filter(p => p.trim() !== '');
        let cur = root;
        for (const p of parts) cur = await cur.getDirectoryHandle(p, {
          create
        });
        return cur;
      };

      // [V19.0 CORE] VFS Drivers (Standardized Interface)
      window.Executor = Executor;

      // VFS Abstraction
      const vfsReadFile = async (path) => {
        if (Deck0.store.getState().project.storageMode === 'VIRTUAL') {
          const content = await IDBHelper.get(path);
          if (content === undefined) throw new Error('File not found');
          return content;
        } else if (Deck0.store.getState().project.storageMode === 'LINK') {
          return await LinkAdapter.read(path);
        } else {
          const parts = path.split('/');
          const name = parts.pop();
          const dir = await getDir(Deck0.store.getState().project.dirHandle, parts.join('/'), false);
          const fh = await dir.getFileHandle(name);
          const f = await fh.getFile();
          return await f.text();
        }
      };

      // [V18] Alias for SnapshotManager and StreamingPatchParser
      const readFileContent = (p) => Deck0.vfs.read(p);


      const getGeminiRawTextFallback = async () => {
        const lastChunk = Deck0Effects.dom.qsa(DECK0_SELECTORS.HOST_MS_TEXT_CHUNK).pop();
        if (!lastChunk) return null;
        const turn = lastChunk.closest(DECK0_SELECTORS.CHAT_TURN);
        if (!turn) return null;
        const editBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.HOST_TURN_EDIT_ICON, turn);
        if (editBtn && editBtn.textContent.trim() === 'edit') editBtn.closest('button').click();
        await new Promise(r => Deck0Effects.clock.setTimeout(r, 500));
        const ta = Deck0Effects.dom.qs(DECK0_SELECTORS.PROMPT_TEXTAREA_FALLBACK, turn);
        const rawText = ta ? ta.value : null;
        const closeBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.HOST_TURN_CANCEL_BUTTON, turn);
        if (closeBtn) closeBtn.click();
        return rawText;
      };
      const captureProjectState = async (forceAll = false) => {
        const ignoreList = Deck0.store.getState().project.ignoreList;
        const fileSelectionMap = Deck0.store.getState().ui.fileSelectionMap;
        if (!Deck0.store.getState().project.dirHandle) return [];
        const files = [];
        const ignore = ignoreList.split(',').map(s => s.trim()).filter(s => s);

        if (Deck0.store.getState().project.storageMode === 'VIRTUAL' || Deck0.store.getState().project.storageMode === 'LINK') {
          let keys = [];
          if (Deck0.store.getState().project.storageMode === 'VIRTUAL') keys = await IDBHelper.getAllKeys();
          else keys = await LinkAdapter.getTree();

          for (const path of keys) {
            // [FIX] Use segment matching to allow 'build.ts' even if 'build' is ignored
            const parts = path.split('/');
            if (ignore.some(x => parts.includes(x))) continue;

            // [V18.2 Snapshot Safety] If forceAll is true, ignore UI selection state
            if (!forceAll && fileSelectionMap.has(path) && !fileSelectionMap.get(path).checked) continue;
            if (!path.match(/\.(js|ts|vue|jsx|tsx|py|html|css|json|md|txt|java|c|cpp|h|go|rs|php|sql|yaml|xml|gitignore|env|gitkeep)$/)) continue;
            try {
              // Reuse vfsReadFile to handle both logic
              const t = await Deck0.vfs.read(path);
              files.push({
                path,
                content: t
              });
            } catch (e) { }
          }
        } else {
          const traverseRead = async (dir, prefix = '') => {
            for await (const entry of dir.values()) {
              const path = prefix ? `${prefix}/${entry.name}` : entry.name;
              // [FIX] Use segment matching
              const parts = path.split('/');
              if (ignore.some(x => parts.includes(x))) continue;

              if (entry.kind === 'file') {
                // [V18.2 Snapshot Safety] If forceAll is true, ignore UI selection state
                if (!forceAll && fileSelectionMap.has(path) && !fileSelectionMap.get(path).checked) continue;
                if (!entry.name.match(/\.(js|ts|vue|jsx|tsx|py|html|css|json|md|txt|java|c|cpp|h|go|rs|php|sql|yaml|xml|gitignore|env|gitkeep)$/)) continue;
                try {
                  const f = await entry.getFile();
                  const t = await f.text();
                  if (t.indexOf('\0') === -1) files.push({
                    path,
                    content: t
                  });
                } catch (e) { }
              } else if (entry.kind === 'directory') await traverseRead(entry, path);
            }
          };
          await traverseRead(Deck0.store.getState().project.dirHandle);
        }
        return files;
      };

      const createTargetedSnapshot = async (paths) => {
        const snapshot = [];
        for (const path of paths) {
          try {
            const content = await Deck0.vfs.read(path);
            snapshot.push({
              path,
              content,
              status: 'modified'
            });
          } catch (e) {
            snapshot.push({
              path,
              content: null,
              status: 'created'
            });
          }
        }
        return snapshot;
      };

      const formatFileTree = (files) => {
        const paths = files.map(f => f.path).sort();
        const tree = {};
        for (const path of paths) {
          const parts = path.split('/');
          let cur = tree;
          for (const part of parts) {
            if (!cur[part]) cur[part] = {};
            cur = cur[part];
          }
        }
        let output = '### PROJECT STRUCTURE\n```text\n.\n';
        const render = (node, prefix = '') => {
          const entries = Object.keys(node).sort((a, b) => {
            const aIsLeaf = Object.keys(node[a]).length === 0;
            const bIsLeaf = Object.keys(node[b]).length === 0;
            if (aIsLeaf === bIsLeaf) return a.localeCompare(b);
            return aIsLeaf ? 1 : -1;
          });
          entries.forEach((entry, index) => {
            const isLast = index === entries.length - 1;
            const connector = isLast ? '└── ' : '├── ';
            output += `${prefix}${connector}${entry}\n`;
            const childPrefix = prefix + (isLast ? '    ' : '│   ');
            render(node[entry], childPrefix);
          });
        };
        render(tree);
        output += '```\n\n';
        return output;
      };

      // [V19.0 CORE] StreamingParser (T3-2 - Pure Logic)
      // Decoupled state machine for Deck0 Protocol.
      class StreamingParser {
        constructor() {
          this.state = 0; // IDLE
          this.buffer = '';
          this.currentPlan = null;
          this.currentOp = null;
          this.currentSearch = '';
          this.currentReplace = '';
          this.listeners = new Set();
        }

        reset() {
          this.state = 0;
          this.buffer = '';
          this.currentPlan = null;
          this.currentOp = null;
          this.currentSearch = '';
          this.currentReplace = '';
        }

        on(event, fn) {
          this.listeners.add({ event, fn });
          return () => this.listeners.delete({ event, fn });
        }

        emit(event, data) {
          this.listeners.forEach(l => {
            if (l.event === event || l.event === '*') l.fn(event, data);
          });
        }

        feed(chunk) {
          this.buffer += chunk;
          this._parse();
        }

        _parse() {
          let iterations = 0;
          const maxIterations = 1000;
          // States: 0=IDLE, 1=IN_PLAN, 2=IN_OP, 3=IN_SEARCH, 4=AFTER_SEARCH (Unused), 5=IN_REPLACE

          while (iterations++ < maxIterations) {
            let consumed = false;

            // Safety: Wait for newline or sufficient length
            if (!this.buffer.includes('\n') && this.buffer.length < 100) break;

            // 0: IDLE
            if (this.state === 0) {
              const planMatch = this.buffer.match(/\[\[DECK0_PLAN_START:([\w\-]+)\]\]/);
              if (planMatch) {
                const idx = this.buffer.indexOf(planMatch[0]);
                this.buffer = this.buffer.slice(idx + planMatch[0].length);
                this.currentPlan = { id: planMatch[1] };
                this.state = 1; // IN_PLAN
                this.emit('PLAN_START', this.currentPlan);
                consumed = true;
              }
            }

            // 1: IN_PLAN
            else if (this.state === 1) {
              // Check PLAN_END
              const endMatch = this.buffer.match(/\[\[DECK0_PLAN_END:([\w\-]+)\]\]/);
              if (endMatch) {
                const idx = this.buffer.indexOf(endMatch[0]);
                this.buffer = this.buffer.slice(idx + endMatch[0].length);
                this.emit('PLAN_END', { id: endMatch[1] });
                this.currentPlan = null;
                this.state = 0; // IDLE
                consumed = true;
                continue;
              }

              // Check OP
              const opMatch = this.buffer.match(/###\s*DECK0_OP:\s*(?:\[(WRITE|PATCH|DELETE)\]|(WRITE|PATCH|DELETE))\s*([^\n\r]+)\r?\n/i);
              if (opMatch) {
                const idx = this.buffer.indexOf(opMatch[0]);
                this.buffer = this.buffer.slice(idx + opMatch[0].length);

                // Sanitization (Mirroring existing logic)
                let p = (opMatch[3] || '').trim();
                p = p.replace(/\s+>.*$/, '').trim();
                p = p.replace(/\s+\|.*$/, '').trim();
                p = p.replace(/#L\d+(?:C\d+)?$/i, '').trim();
                p = p.replace(/:(\d+)(?::\d+)?$/i, '').trim();
                p = p.replace(/^["']+|["']+$/g, '').replace(/^`+|`+$/g, '').trim();
                p = p.replace(/[),.;]+$/g, '').trim();
                // Slashes
                p = p.replace(/\\/g, '/').trim();

                this.currentOp = {
                  type: (opMatch[1] || opMatch[2] || '').toUpperCase(),
                  path: p
                };
                this.state = 2; // IN_OP
                this.emit('OP_START', this.currentOp);
                consumed = true;
              }
            }

            // 2: IN_OP
            else if (this.state === 2) {
              const nextOpMatch = this.buffer.match(/(?:^|\n)[ \t]*###\s*DECK0_OP:\s*/i);
              const planEndMatch = this.buffer.match(/\[\[DECK0_PLAN_END:/);

              // Check Search Fence (For Patch)
              const searchMatch = this.buffer.match(/(?:\n|^)[ \t]*<<<<<<[ \t]*SEARCH[ \t]*\r?\n/i);
              if (searchMatch) {
                const idx = this.buffer.indexOf(searchMatch[0]);
                this.buffer = this.buffer.slice(idx + searchMatch[0].length);
                this.currentSearch = '';
                this.state = 3; // IN_SEARCH
                consumed = true;
                continue;
              }

              // Check Code Block (For Write)
              const codeBlockMatch = this.buffer.match(/```(\w*)\r?\n/);
              if (codeBlockMatch && this.currentOp && this.currentOp.type === 'WRITE') {
                const startIdx = this.buffer.indexOf(codeBlockMatch[0]) + codeBlockMatch[0].length;
                const endMatch = this.buffer.slice(startIdx).match(/\n```/);
                if (!endMatch) break; // Wait for more
                if (endMatch) {
                  const code = this.buffer.slice(startIdx, startIdx + endMatch.index);
                  this.buffer = this.buffer.slice(startIdx + endMatch.index + endMatch[0].length);
                  this.emit('OP_HUNK', {
                    ...this.currentOp,
                    content: code
                  });
                  this.currentOp = null; // Write is atomic
                  this.state = 1; // Back to Plan
                  consumed = true;
                  continue;
                }
              }

              // End of Op?
              if (nextOpMatch || planEndMatch) {
                if (this.currentOp) {
                  // Delete is atomic (no body)
                  if (this.currentOp.type === 'DELETE') {
                    this.emit('OP_HUNK', { ...this.currentOp });
                  }
                }
                this.currentOp = null;
                this.state = 1; // Back to Plan
                consumed = true;
                continue;
              }
            }

            // 3: IN_SEARCH
            else if (this.state === 3) {
              const sepMatch = this.buffer.match(/(?:\n|^)[ \t]*======[ \t]*\r?\n/);
              if (sepMatch) {
                const idx = this.buffer.indexOf(sepMatch[0]);
                this.currentSearch = this.buffer.slice(0, idx);
                this.buffer = this.buffer.slice(idx + sepMatch[0].length);
                this.currentReplace = '';
                this.state = 5; // IN_REPLACE
                consumed = true;
              }
            }

            // 5: IN_REPLACE
            else if (this.state === 5) {
              const endMatch = this.buffer.match(/(?:\n|^)[ \t]*>>>>>>[ \t]*REPLACE[ \t]*(?:\r?\n|$)/i);
              if (endMatch) {
                const idx = this.buffer.indexOf(endMatch[0]);
                this.currentReplace = this.buffer.slice(0, idx);
                this.buffer = this.buffer.slice(idx + endMatch[0].length);

                this.emit('OP_HUNK', {
                  ...this.currentOp,
                  search: this.currentSearch,
                  replace: this.currentReplace
                });

                this.currentSearch = '';
                this.currentReplace = '';
                this.state = 2; // Back to Op (Wait for more hunks or end)
                consumed = true;
              }
            }

            if (!consumed) break;
          }
        }
      }
      // Expose for Phase 4/5
      window.StreamingParser = StreamingParser;

      // ===================== DECK0 PROTOCOL =====================
      // 协议层：网络传输/流解码/协议引擎
      // - Transport: 事件总线 (emit/on: RAW_FRAME/THOUGHT/ANSWER/EOS)
      // - GeminiProvider: Gemini 流解码器
      // - DomOnlyProvider: 降级兜底
      // - ProtocolEngine: 协议解析 + 流式执行 + EOS Safety

      // [V19.0 INFRA] Transport Layer (T5-1)
      const Transport = {
        listeners: new Set(),
        on(event, fn) {
          const l = { event, fn };
          this.listeners.add(l);
          return () => this.listeners.delete(l);
        },
        emit(event, payload) {
          this.listeners.forEach(l => {
            if (l.event === event) l.fn(payload);
            else if (l.event === '*') l.fn(event, payload);
          });
        }
      };
      window.Transport = Transport;

      // [V19.0 CORE] Gemini Provider (T5-2)
      // Decoupled Protocol Parser for Gemini (Safe Stream)
      const GeminiProvider = {
        _streamCarry: '',

        init() {
          Transport.on('START', () => { this._streamCarry = ''; });
          Transport.on('RAW_FRAME', (chunk) => this.feed(chunk));
        },

        feed(newBytes) {
          if (!newBytes) return;
          const buffer = this._streamCarry + newBytes;

          // [V18.1] Enhanced Protocol Regex (Identical to PhantomXHR)
          const regex = /\[\s*null\s*,\s*"((?:[^"\\]|\\.)*)"((?:,[\s\S]*?)?)\]/g;

          let match;
          let lastConsumed = 0;

          while ((match = regex.exec(buffer)) !== null) {
            lastConsumed = regex.lastIndex;
            const contentInner = match[1];
            const tailMetadata = match[2]; // e.g. ", null, ..., 1"

            let rawContent = "";
            try {
              rawContent = JSON.parse(`"${contentInner}"`);
            } catch (e) { continue; }

            // 1. EOS Signal
            if (rawContent === "") {
              Transport.emit('EOS', {});
              continue;
            }

            // 2. Content Dispatch
            if (rawContent) {
              const isReasoning = tailMetadata && tailMetadata.trim().endsWith('1');
              if (isReasoning) {
                Transport.emit('THOUGHT', { content: rawContent });
              } else {
                Transport.emit('ANSWER', { content: rawContent });
              }
            }
          }

          this._streamCarry = buffer.slice(lastConsumed);
          // Safety cap for carry buffer (should rarely be hit if stream is healthy)
          if (this._streamCarry.length > 50000) this._streamCarry = this._streamCarry.slice(-50000);
        }
      };
      window.GeminiProvider = GeminiProvider;

      // [V19.0 CORE] DomOnly Provider (T5-2)
      // Fallback Scraper when Network/XHR fails
      const DomOnlyProvider = {
        fetchFullText: async () => {
          // Heuristic: Find the last text chunk in the chat
          let turn = Deck0Effects.dom.qsa(DECK0_SELECTORS.HOST_MS_TEXT_CHUNK).pop();
          if (!turn) return null;
          turn = turn.closest(DECK0_SELECTORS.CHAT_TURN);
          if (!turn) return null;

          // Strategy A: Try to open "Edit" mode to get raw Markdown (Best Quality)
          const editBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.HOST_TURN_EDIT_ICON, turn);
          if (editBtn && editBtn.textContent.trim() === 'edit') {
            editBtn.closest('button').click();

            // Wait for UI transition
            await new Promise(r => Deck0Effects.clock.setTimeout(r, 500));

            const ta = Deck0Effects.dom.qs(DECK0_SELECTORS.PROMPT_TEXTAREA_FALLBACK, turn);
            const rawText = ta ? ta.value : null;

            // Cancel Edit Mode
            const closeBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.HOST_TURN_CANCEL_BUTTON, turn);
            if (closeBtn) closeBtn.click();

            if (rawText) return rawText;
          }

          // Strategy B: InnerText (Fallback, might lose some formatting)
          console.warn('[DomOnlyProvider] Fallback to innerText scraping');
          return turn.innerText;
        }
      };
      window.DomOnlyProvider = DomOnlyProvider;

      // [V19.0 CORE] Orchestrator (T5-3)
      const Orchestrator = {
        mountProject: async () => {
          const state = store.getState();
          // [Fix] Respect Hybrid Mode (Store vs Local)
          const mode = state.project.storageMode;

          try {
            // 1. VIRTUAL / LINK MODES
            if (mode === 'VIRTUAL') {
              await IDBHelper.init();
              Deck0.store.mutate('project', p => { p.dirHandle = { name: 'Virtual_Drive', kind: 'directory' }; }); // Trigger Proxy
              log(`Virtual Disk Mounted.`, 'success');
              await refreshFileTree();
              return true;
            }
            if (mode === 'LINK') {
              if (_linkIntervalId) Deck0Effects.clock.clearInterval(_linkIntervalId);
              const status = await LinkAdapter.check();
              Deck0.store.mutate('project', p => { p.dirHandle = { name: status.cwd || 'Linked_Project', kind: 'directory' }; }); // Trigger Proxy
              log(`🔗 Connected to Deck0 Link!`, 'success');
              log(`📂 CWD: ${status.cwd}`, 'info');
              // Auto-Refresh (Legacy parity)
              _linkIntervalId = Deck0Effects.clock.setInterval(() => {
                refreshFileTree(true).catch(e => console.warn('Auto-Refresh Failed', e));
              }, 10000);
              await refreshFileTree();
              return true;
            }

            // 2. LOCAL MODE (Permission & Probe)
            // [Fix] Read from Proxy to get correct truth in hybrid mode
            let handle = window.projectDirHandle;

            // A. Check Handle Existence
            if (!handle) {
              return await Orchestrator._picker();
            }

            // B. Permission Query
            if (handle.queryPermission) {
              const perm = await handle.queryPermission({ mode: 'readwrite' });
              if (perm === 'granted') {
                // Double check with probe to be sure
              } else if (perm === 'prompt') {
                const newPerm = await handle.requestPermission({ mode: 'readwrite' });
                if (newPerm !== 'granted') return false;
              } else {
                // Denied or other state
                return await Orchestrator._picker();
              }
            }

            // C. Write Probe (Bypass Executor)
            try {
              const probePath = '.deck0_probe';
              // Use LocalVfs directly to avoid UI sync
              await LocalVfs.write(probePath, 'probe');
              await LocalVfs.del(probePath);
              log(`Project remounted: ${handle.name}`, 'success');
              await refreshFileTree();
              return true;
            } catch (e) {
              // Probe failed, handle likely stale
              console.warn('[Orchestrator] Probe failed, prompting picker...', e);
              return await Orchestrator._picker();
            }

          } catch (e) {
            log(`Mount Error: ${e.message}`, 'error');
            return false;
          }
        },

        _picker: async () => {
          try {
            const handle = await window.showDirectoryPicker({ mode: 'readwrite' });
            Deck0.store.mutate('project', p => { p.dirHandle = handle; }); // Trigger Proxy
            log(`Project mounted: ${handle.name}`, 'success');
            await refreshFileTree();
            return true;
          } catch (e) {
            if (e.name !== 'AbortError') log(`Picker Error: ${e.message}`, 'error');
            return false;
          }
        }
      };
      window.Orchestrator = Orchestrator;

      // [V19.0 CORE] Protocol Engine (S4)
      // Owns: protocol parsing + stream-to-write + EOS finalization.
      // UI modules should observe state and render; they must not parse/execute.
      const ProtocolEngine = {
        _startedRunId: null,

        init: () => {
          if (!window.Transport) return;
          Transport.on('START', (meta) => ProtocolEngine.handleStart(meta));
          Transport.on('ANSWER', (d) => ProtocolEngine.handleAnswer(d && d.content != null ? d.content : d));
          Transport.on('EOS', (meta) => ProtocolEngine.handleEOS(meta));
        },

        _getCurrentRunId: () => {
          try { return Deck0.store.getState().runtime ? (Number(Deck0.store.getState().runtime.runId) || 0) : 0; } catch (e) { return 0; }
        },

        handleStart: (_meta = null) => {
          const metaRid = (_meta && typeof _meta === 'object' && _meta.runId != null) ? (Number(_meta.runId) || 0) : 0;
          const rid = metaRid || ProtocolEngine._getCurrentRunId();
          if (rid && ProtocolEngine._startedRunId === rid) return;
          ProtocolEngine._startedRunId = rid || null;

          // Idempotent safety reset (prevents cross-run bleed).
          Deck0Actions.protocol.resetForNewRun();
          try { StreamingPatchParser.reset(); } catch (e) { }
        },

        handleAnswer: (text) => {
          if (!text) return;

          // NOTE: Keep ordering identical to legacy behavior.
          const full = Deck0Actions.protocol.appendAnswerChunk(text);
          parseDeck0Protocol(full);
          StreamingPatchParser.feed(text);
        },

        handleEOS: async (meta = null) => {
          log('🏁 Protocol EOS Detected.', 'success');

          // [V19.0] Finalize gating (prevent double finalize vs PhantomXHR loadend fallback)
          const currentRid = ProtocolEngine._getCurrentRunId();
          const rid = (meta && typeof meta === 'object' && meta.runId != null) ? (Number(meta.runId) || 0) : currentRid;
          if (rid && currentRid && rid !== currentRid) return;

          try {
            if (Deck0.store.getState().runtime && Deck0.store.getState().runtime.finalizeDoneRunId === rid) return;
          } catch (e) { }

          Deck0Actions.runtime.setFinalizeDoneRunId(rid);
          Deck0Actions.runtime.markGenerationDone();

          await StreamingPatchParser.finalCorrection(Deck0.store.getState().protocol.lastAnswerMarkdown);
          scheduleUiSync('generation-complete', { forceTree: true, forceEditor: true });

          // Auto-Diff
          if (Deck0.store.getState().runtime.lastAutoDiffRunId !== rid) {
            Deck0Actions.runtime.setLastAutoDiffRunId(rid);
            Deck0Effects.clock.setTimeout(async () => {
              if (Deck0.store.getState().session.syncList && Deck0.store.getState().session.syncList.length > 0) {
                if (!Deck0.store.getState().legacy.DECK0_VIEW || !Deck0.store.getState().legacy.DECK0_VIEW.mode) {
                  log('🔎 Auto-opening Diff Review...', 'purple');
                  await enterDiffReviewMode();
                }
              }
            }, 300);
          }

          Deck0Actions.runtime.markAutoExecutionDone();
          if (typeof updateSendBtnState === 'function') updateSendBtnState(false);
        }
      };
      window.ProtocolEngine = ProtocolEngine;

      // [V19.0 UI] Bridge UI Adapter (T5-4)
      const BridgeUIAdapter = {
        thinkingUI: null,
        answerBubble: null,

        init: () => {
          if (!window.Transport) return;
          Transport.on('START', () => {
            BridgeUIAdapter.thinkingUI = null;
            BridgeUIAdapter.answerBubble = null;
            const panelStream = Deck0.ui.refs.streamView;
            if (panelStream) panelStream.textContent = "";
          });
          Transport.on('THOUGHT', (d) => BridgeUIAdapter.handleThought(d.content));
          Transport.on('ANSWER', (d) => BridgeUIAdapter.handleAnswer(d.content));
          Transport.on('EOS', (d) => BridgeUIAdapter.handleEOS(d));
        },

        handleThought: (text) => {
          if (!BridgeUIAdapter.thinkingUI) {
            const container = el('div', { cls: 'deck0-thinking-container', parent: chatHistory });
            const header = el('div', { cls: 'deck0-thinking-header', parent: container });
            const arrow = el('span', { cls: 'deck0-thinking-arrow', text: '▼', parent: header });
            el('span', { text: 'Thinking Process', parent: header });
            const content = el('div', { cls: 'deck0-thinking-content', parent: container });

            content.classList.add('open');
            header.classList.add('active');
            arrow.style.transform = 'rotate(0deg)';

            header.onclick = () => {
              const isOpen = content.classList.toggle('open');
              header.classList.toggle('active');
              arrow.style.transform = isOpen ? 'rotate(0deg)' : 'rotate(-90deg)';
            };

            BridgeUIAdapter.thinkingUI = { container, header, content, arrow };
            chatHistory.scrollTop = chatHistory.scrollHeight;
          }
          SmoothTyper.push(text, BridgeUIAdapter.thinkingUI.content);
        },

        handleAnswer: (text) => {
          // Collapse Thinking if active
          if (BridgeUIAdapter.thinkingUI) {
            if (BridgeUIAdapter.thinkingUI.content.classList.contains('open')) {
              BridgeUIAdapter.thinkingUI.content.classList.remove('open');
              BridgeUIAdapter.thinkingUI.header.classList.remove('active');
              BridgeUIAdapter.thinkingUI.arrow.style.transform = 'rotate(-90deg)';
            }
          }

          if (!BridgeUIAdapter.answerBubble || BridgeUIAdapter.answerBubble.parentElement !== chatHistory.lastElementChild) {
            const wrapper = addChatMessage('assistant', '');
            BridgeUIAdapter.answerBubble = wrapper.querySelector(DECK0_SELECTORS.UI_CHAT_MESSAGE_CONTENT);
          }
          SmoothTyper.push(text, BridgeUIAdapter.answerBubble);

          // Stream View
          const panelStream = Deck0.ui.refs.streamView;
          if (panelStream) {
            panelStream.textContent += text;
            if (panelStream.style.display !== 'none' && panelStream.parentElement) {
              panelStream.parentElement.scrollTop = panelStream.parentElement.scrollHeight;
            }
          }
        },

        handleEOS: async (_meta = null) => {
          // UI-only cleanup. Protocol finalization is owned by ProtocolEngine.
          BridgeUIAdapter.thinkingUI = null;
          BridgeUIAdapter.answerBubble = null;
        }
      };

      // [P6-BEHAVIOR] Shadow UI Bridge Adapter (Behavior Layer)
      const ShadowBridgeUIAdapter = {
        thinkingUI: null,
        answerBubble: null,

        init: () => {
          if (!window.Transport) return;

          Transport.on('START', () => {
            if (!Deck0.ui.shadow.enabled) return;
            ShadowBridgeUIAdapter.thinkingUI = null;
            ShadowBridgeUIAdapter.answerBubble = null;
          });

          Transport.on('THOUGHT', (d) => {
            if (!Deck0.ui.shadow.enabled) return;
            if (!ShadowBridgeUIAdapter.thinkingUI) {
              Deck0.ui.shadow.addThinkingBubble();
              ShadowBridgeUIAdapter.thinkingUI = Deck0.ui.shadow.refs.thinkingContent;
            }
            Deck0.ui.shadow.appendThinkingContent(d.content);
            Deck0.ui.shadow.scrollToBottom(); // [P6-SCROLL] Auto-scroll
          });

          Transport.on('ANSWER', (d) => {
            if (!Deck0.ui.shadow.enabled) return;

            // Collapse thinking if active
            if (ShadowBridgeUIAdapter.thinkingUI) {
              Deck0.ui.shadow.collapseThinking();
            }

            // Create or reuse answer bubble
            if (!ShadowBridgeUIAdapter.answerBubble) {
              ShadowBridgeUIAdapter.answerBubble = Deck0.ui.shadow.addChatMessage('assistant', '');
            }

            // Append text
            const contentEl = ShadowBridgeUIAdapter.answerBubble?.querySelector('.deck0-msg-content');
            if (contentEl) {
              contentEl.textContent += d.content;
              Deck0.ui.shadow.scrollToBottom(); // [P6-SCROLL] Auto-scroll
            }
          });

          Transport.on('EOS', () => {
            if (!Deck0.ui.shadow.enabled) return;
            ShadowBridgeUIAdapter.thinkingUI = null;
            ShadowBridgeUIAdapter.answerBubble = null;
          });
        },

        // Sync chat history from original UI
        syncHistory: () => {
          // [FIX] Reset TaskList on new run
          if (Deck0.ui.shadow.renderTaskList) Deck0.ui.shadow.renderTaskList();
          if (!Deck0.ui.shadow.enabled) return;

          const chatHistory = document.querySelector('.deck0-chat-history:not(.deck0-shadow-stream-view)');
          if (!chatHistory) return;

          // Clear shadow chat
          const shadowChat = Deck0.ui.shadow.refs.chatHistory;
          if (shadowChat) shadowChat.replaceChildren();

          // Clone user bubble (first child without class)
          const firstChild = chatHistory.children[0];
          if (firstChild && !firstChild.className) {
            const userContent = firstChild.textContent || '';
            if (userContent.trim()) {
              Deck0.ui.shadow.addChatMessage('user', userContent);
            }
          }

          // Clone thinking bubble if exists
          const thinkingContainer = chatHistory.querySelector('.deck0-thinking-container');
          if (thinkingContainer) {
            const thinkingContent = thinkingContainer.querySelector('.deck0-thinking-content')?.textContent || '';
            if (thinkingContent) {
              Deck0.ui.shadow.addThinkingBubble();
              Deck0.ui.shadow.appendThinkingContent(thinkingContent);

              // Match open/closed state
              const isOpen = thinkingContainer.querySelector('.deck0-thinking-content')?.classList.contains('open');
              if (!isOpen) {
                Deck0.ui.shadow.collapseThinking();
              }
            }
          }

          // Clone assistant bubble
          const assistantBubble = chatHistory.querySelector('.deck0-assistant-bubble');
          if (assistantBubble) {
            const msgContent = assistantBubble.querySelector('.deck0-msg-content');
            const content = msgContent?.textContent || '';
            if (content.trim()) {
              Deck0.ui.shadow.addChatMessage('assistant', content);
            }
          }
        }
      };

      // [V19.0 CORE] PromptPack Engine (T3-1)
      const PromptPack = {
        // Assets
        experts: EXPERTS,
        templates: _T,
        protocol: DECK0_PROTOCOL_TEMPLATE,

        // System Prompt Builder (Replaces global getSystemPrompt logic)
        getSystemPrompt: (expertId) => {
          const id = expertId || Deck0.store.getState().settings.expertId || 'ARCHITECT';
          const expert = EXPERTS[id] || EXPERTS['ARCHITECT'];
          const fullTemplate = `${expert.persona}\n\n${DECK0_PROTOCOL_TEMPLATE}`;
          // [DECK0 CRITICAL] Replacements must be ordered DESCENDING
          return fullTemplate
            .replace(/#12/g, _T.ce)
            .replace(/#10/g, _T.rp)
            .replace(/#8/g, _T.xml)
            .replace(/#7/g, _T.op)
            .replace(/#6/g, _T.td)
            .replace(/#5/g, _T.p_e)
            .replace(/#4/g, _T.p_s)
            .replace(/#3/g, _T.m_e)
            .replace(/#2/g, _T.m_s)
            .replace(/#1/g, _T.ga);
        },

        // Context Assembler (Replaces prepareDeck0Payload logic)
        create: (userInstruction, snapshot, options = {}) => {
          const expertId = options.expertId || Deck0.store.getState().settings.expertId || 'ARCHITECT';
          const model = options.model || Deck0.store.getState().settings.model || 'gemini-3-pro-preview';
          const includeContext = options.includeContext !== undefined ? options.includeContext : true;

          let contextStr = "";
          if (includeContext) {
            contextStr = `[PROJECT CONTEXT - ${snapshot.length} FILES]\n`;
            if (typeof formatFileTree === 'function') {
              contextStr += formatFileTree(snapshot);
            }
            snapshot.forEach(f => {
              const ext = f.path.split('.').pop();
              contextStr += `### File: ${f.path}\n\`\`\`${ext}\n${f.content}\n\`\`\`\n`;
            });
          }

          return {
            systemPrompt: PromptPack.getSystemPrompt(expertId),
            projectContextForModel: contextStr,
            baselineSnapshotFull: snapshot,
            userInstruction: userInstruction,
            model: model,
            expertId: expertId
          };
        },

        // Serialization for Legacy Transport (Textarea Injection)
        toString: (pack) => {
          return `${pack.projectContextForModel}--------------------------------------------------\n[USER INSTRUCTIONS]\n${pack.userInstruction}\n\n`;
        }
      };
      // Expose for debugging/future modules
      window.PromptPack = PromptPack;

      const runPipeline = async () => {
        const userInstruction = inputArea.value.trim();
        if (!userInstruction) return log("Instruction empty.", 'warn');
        if (!Deck0.store.getState().project.dirHandle) return log("Mount project first.", 'error');

        Deck0Actions.session.setLastUserInstruction(userInstruction);

        // [V19.0 FIX] Start a new run when using runPipeline (keep parity with prepareDeck0Payload)
        Deck0Actions.runtime.beginRun();
        Deck0Actions.runtime.resetAutoDiffLocks();
        console.log('[Deck0][Run] runPipeline start runId=', Deck0.store.getState().runtime.runId);
        log(`[Run] runPipeline start runId=${Deck0.store.getState().runtime.runId}`, 'info');

        // [UX] Immediate List Reset
        const tasksBody = Deck0.ui.refs.tasksBody;
        if (tasksBody) {
          tasksBody.replaceChildren();
          if (tasksCount) tasksCount.textContent = 'TASKS (0)';
        }
        const filesBody = Deck0.ui.refs.filesBody;
        if (filesBody) {
          filesBody.replaceChildren();
          if (filesCount) filesCount.textContent = 'CHANGED FILES (0)';
        }

        // [V18.3] Clear Chat Logic
        if (Deck0.store.getState().settings.clearChat && chatHistory) {
          chatHistory.replaceChildren();
        }

        addChatMessage('user', userInstruction);

        // [P6-SYNC] Sync User Message to Shadow UI
        if (Deck0.ui.shadow?.enabled && Deck0.ui.shadow.addChatMessage) {
          Deck0.ui.shadow.addChatMessage('user', userInstruction);
        }

        inputArea.value = ''; // Clear UI
        inputArea.dispatchEvent(new Event('input', { bubbles: true })); // [Fix] Reset height

        document.body.classList.add('deck0-automation-active');

        // [DECK0 INJECTION] Generate the clean prompt at runtime
        const finalSystemPrompt = getSystemPrompt();

        try {
          if (Deck0.store.getState().settings.clearChat) {
            log("Automation: Clearing history...", 'warn');
            let turns = Deck0Effects.dom.qsa(DECK0_SELECTORS.CHAT_TURN);
            for (let i = turns.length - 1; i >= 0; i--) {
              try {
                const menuBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.CHAT_TURN_MORE_OPTIONS_TOOLTIP_BUTTON, turns[i]) || Deck0Effects.dom.qsa(DECK0_SELECTORS.ALL_BUTTONS, turns[i]).find(b => String(b.textContent || '').includes('more_vert'));
                if (!menuBtn) continue;
                menuBtn.click();
                await new Promise(r => Deck0Effects.clock.setTimeout(r, 150));
                const delBtn = Deck0Effects.dom.qsa(DECK0_SELECTORS.MAT_MENU_ITEM).find(item => item.textContent.trim().includes('Delete'));
                if (delBtn) {
                  delBtn.click();
                  await new Promise(r => Deck0Effects.clock.setTimeout(r, 250));
                } else {
                  document.body.click();
                }
              } catch (e) { }
            }
          } else {
            log("Automation: Chat history preserved.", 'info');
          }

          // [V18 Interaction Mode Check]
          if (Deck0.store.getState().settings.interactionMode !== 'PROTOCOL') {
            const panelBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.SYSTEM_INSTRUCTIONS_PANEL_BUTTON);
            if (panelBtn) {
              panelBtn.click();
              let textarea = null,
                attempts = 0;
              while (attempts < 20) {
                textarea = Deck0Effects.dom.qs(DECK0_SELECTORS.SYSTEM_PROMPT_DIALOG_TEXTAREA);
                if (textarea) break;
                await new Promise(r => Deck0Effects.clock.setTimeout(r, 100));
                attempts++;
              }

              // Compare using the Generated Prompt
              if (textarea && textarea.value !== finalSystemPrompt) {
                textarea.focus();
                textarea.value = finalSystemPrompt;
                textarea.dispatchEvent(new Event('input', {
                  bubbles: true
                }));
                await new Promise(r => Deck0Effects.clock.setTimeout(r, 300));
              }
              const closeBtn = Deck0Effects.dom.qs(DECK0_SELECTORS.SYSTEM_PROMPT_DIALOG_CLOSE_ICON) || Deck0Effects.dom.qs(DECK0_SELECTORS.SYSTEM_PROMPT_DIALOG_CLOSE_TEST);
              if (closeBtn) {
                closeBtn.click();
                await new Promise(r => Deck0Effects.clock.setTimeout(r, 200));
              }
            }
          } else {
            log("Protocol Mode Active: Skipping UI System Prompt Injection.", 'purple');
          }
        } catch (e) {
          log(`Automation Warning: ${e.message}`, 'error');
        } finally {
          document.body.classList.remove('deck0-automation-active');
        }

        if (typeof Deck0.store.getState().settings.includeCodeContext !== 'undefined' && !Deck0.store.getState().settings.includeCodeContext) {
          Deck0.store.mutate('project', p => { p.baselineSnapshot = []; });
          log("Code Context Skipped (Toggle OFF)", 'info');
        } else {
          const _snap = await captureProjectState(); Deck0.store.mutate('project', p => { p.baselineSnapshot = _snap; });
        }
        Deck0Actions.session.resetPendingChanges();
        try {
          const convId = SnapshotManager.currentConversationId;
          if (convId) SnapshotManager.conversations.delete(convId);
          SnapshotManager.currentConversationId = null;
        } catch (e) { }
        let p = `[PROJECT CONTEXT - ${Deck0.store.getState().project.baselineSnapshot.length} FILES]\n`;
        p += formatFileTree(Deck0.store.getState().project.baselineSnapshot);
        Deck0.store.getState().project.baselineSnapshot.forEach(f => {
          p += `### File: ${f.path}\n\`\`\`${f.path.split('.').pop()}\n${f.content}\n\`\`\`\n`;
        });
        p += `--------------------------------------------------\n[USER INSTRUCTIONS]\n${userInstruction}\n\n`;
        const geminiInput = Deck0Effects.dom.findPromptTextarea();
        if (geminiInput) {
          Deck0Actions.protocol.resetForNewRun();

          setBadgeState('IDLE', '#555');

          // [V16.9] UX: Switch to Stream immediately
          // switchTermTab('stream'); // [MOD] Disabled auto-switch

          if (updateSendBtnState) updateSendBtnState(true);
          geminiInput.value = p;
          geminiInput.dispatchEvent(new Event('input', {
            bubbles: true
          }));
          Deck0Effects.clock.setTimeout(() => {
            const b = Deck0Effects.dom.findSendButton();
            if (b) b.click();
            if (updateSendBtnState) updateSendBtnState(true); // Set STOP state
            log("Task sent. Waiting for AI to think...", 'info');
          }, 500);
        }
      };

      // [V16.9 CORE: FENCE PARSER] - Replaces XML Parser
      // [FIXED V4] Ultimate Stability Patch
      // 1. Strict Anchoring: Prevents // comments from triggering fences.
      // 2. Length Guard: (?!=) prevents matching 7+ equals (Git conflicts).
      // 3. Greedy Fix: Changed \\s* to [ \\t]* in End Fence to prevents eating newlines 
      //    needed by the NEXT fence's anchor. Solves "Missing 2nd Fence" bug.
      const parseActions = (text) => {
        const actions = [];
        text = String(text || '');

        // Ignore the Design section entirely (prevents template/analysis from being parsed as ops)
        try {
          const tdTag = _T.td;
          const tdRegex = new RegExp(`<${tdTag}>[\\s\\S]*?<\\/${tdTag}>`, 'gi');
          text = text.replace(tdRegex, '\n');
        } catch (e) { }

        // Pre-compute fenced code block ranges so we don't treat "### *_OP" inside code as real ops.
        // This fixes phantom operations when the assistant writes code that contains protocol-like lines.
        const codeFenceRanges = [];
        try {
          const fenceBlockRegex = /```[\s\S]*?```/g;
          let fm;
          while ((fm = fenceBlockRegex.exec(text)) !== null) {
            codeFenceRanges.push([fm.index, fm.index + fm[0].length]);
          }
        } catch (e) { }
        const isInCodeFence = (idx) => {
          if (!codeFenceRanges.length) return false;
          return codeFenceRanges.some(([s, e]) => idx >= s && idx < e);
        };

        // Dynamic construction to avoid self-parsing
        const fenceStart = _T.xml; // <<<<<< SEARCH
        const fenceMid = _T.rp; // ======
        const fenceEnd = _T.ce; // >>>>>> REPLACE
        const escapeRegExp = (string) => string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');

        // Regex to identify Operation Blocks first
        const opHeaderRegex = /(?:^|\r?\n)[ \t]*###\s*DECK0_OP:\s*(?:\[(WRITE|PATCH|DELETE)\]|(WRITE|PATCH|DELETE))\s*([^\n\r]+)/gi;

        let match;
        const ops = [];

        // Pass 1: Segment the text by Operation Headers
        while ((match = opHeaderRegex.exec(text)) !== null) {
          if (isInCodeFence(match.index)) continue;
          const path = sanitizeOpPath(match[3] || '');
          const skip = isTemplatePlaceholderPath(path) || !isSafeVfsPath(path);
          ops.push({
            type: (match[1] || match[2]).toLowerCase(),
            path,
            skip,
            index: match.index,
            fullMatch: match[0]
          });
        }

        // Pass 2: Process each block content
        for (let i = 0; i < ops.length; i++) {
          const currentOp = ops[i];
          const nextOp = ops[i + 1];
          if (currentOp.skip) continue;

          // Content starts after the header line
          const contentStart = currentOp.index + currentOp.fullMatch.length;
          // Content ends at the start of next OP, or end of text
          const contentEnd = nextOp ? nextOp.index : text.length;

          const blockContent = text.slice(contentStart, contentEnd);

          if (currentOp.type === 'write') {
            const codeBlockRegex = /```[a-zA-Z0-9]*\r?\n([\s\S]*?)\r?\n```/;
            const m = blockContent.match(codeBlockRegex);
            if (m) {
              actions.push({
                type: 'write',
                path: currentOp.path,
                content: m[1],
                index: currentOp.index
              });
            }
          } else if (currentOp.type === 'delete') {
            actions.push({
              type: 'delete',
              path: currentOp.path,
              index: currentOp.index
            });
          } else if (currentOp.type === 'patch') {
            // [V17.4 REGEX - THE "MULTI-BLOCK" FIX]
            // We MUST NOT use \\s* at the end of the fence, because it eats newlines.
            // We use [ \\t]* to only eat spaces on the same line, preserving the \n
            // for the next fence's anchor match.

            const fenceRegex = new RegExp(
              // 1. START FENCE: Anchor to newline
              '(?:^|\\r?\\n)[ \\t]*' + escapeRegExp(fenceStart) + '[ \\t]*\\r?\\n' +

              // 2. SEARCH BLOCK (Group 1)
              '([\\s\\S]*?)' +

              // 3. MID SEPARATOR: Anchor + Guard against 7 equals
              '(?:^|\\r?\\n)[ \\t]*' + escapeRegExp(fenceMid) + '(?!=)[ \\t]*\\r?\\n' +

              // 4. REPLACE BLOCK (Group 2)
              '([\\s\\S]*?)' +

              // 5. END FENCE: Anchor + Non-Greedy Trailing Match
              // [CRITICAL CHANGE]: \\s* -> [ \\t]*
              '(?:^|\\r?\\n)[ \\t]*' + escapeRegExp(fenceEnd) + '[ \\t]*(?:$|\\r?\\n)',

              'gi'
            );

            let fenceMatch;
            while ((fenceMatch = fenceRegex.exec(blockContent)) !== null) {
              actions.push({
                type: 'patch',
                path: currentOp.path,
                searchBlock: fenceMatch[1],
                replaceBlock: fenceMatch[2],
                index: currentOp.index + fenceMatch.index
              });
            }
          }
        }

        return actions.sort((a, b) => a.index - b.index);
      };

      // [V16.9 CORE: FENCE PATCHER] - Smart Indentation-Aware Replacement
      // [FIXED] Adds "Flexible Match" to handle cases where AI hallucinates indentation.
      const corePatch = (currentContent, action) => {
        const searchBlock = action.searchBlock;
        const replaceBlock = action.replaceBlock;

        if (!searchBlock) return null;

        // 1. Level 1: Exact Match (Fastest & Safest)
        if (currentContent.includes(searchBlock)) {
          return currentContent.replace(searchBlock, replaceBlock);
        }

        // 2. Level 2: Trimmed Match (Ignores leading/trailing newlines of the block)
        const cleanSearch = searchBlock.trim();
        if (currentContent.includes(cleanSearch)) {
          return currentContent.replace(cleanSearch, replaceBlock);
        }

        // 3. Level 3: Flexible Indentation Match (The "AI Fixer")
        // This handles cases where AI outputs 12 spaces indentation but file has 8.
        // It constructs a regex that ignores whitespace at the start/end of each line.
        try {
          const lines = cleanSearch.split(/\r?\n/);
          // Create a regex pattern: match each line, but allow any amount of indentation (\s*)
          const regexParts = lines.map(line => {
            const trimmed = line.trim();
            if (!trimmed) return '\\s*'; // Empty lines match any whitespace lines
            // Escape special regex characters (brackets, dots, etc.)
            const escaped = trimmed.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
            // Allow horizontal whitespace [ \t]* before and after the content
            return '[ \\t]*' + escaped + '[ \\t]*';
          });

          // Join lines with flexible newlines
          const regexStr = regexParts.join('\\r?\\n');
          const flexRegex = new RegExp(regexStr);

          const match = currentContent.match(flexRegex);
          if (match) {
            // Found it! Replace the matched segment (whatever its indentation was)
            // with the AI's replacement block.
            console.log(`[DiffEngine] Flexible match found for: ${cleanSearch.substring(0, 20)}...`);
            return currentContent.replace(match[0], replaceBlock);
          }
        } catch (e) {
          console.warn('[DiffEngine] Flexible regex construction failed:', e);
        }

        // 4. Level 4: Already-Applied Match (Idempotency)
        // If the replacement block already exists in the file, treat as a no-op success.
        const cleanReplace = (replaceBlock || '').trim();
        if (cleanReplace && currentContent.includes(cleanReplace)) {
          console.log(`[DiffEngine] Patch skipped (already applied): ${cleanSearch.substring(0, 20)}...`);
          return currentContent;
        }

        console.warn(`[DiffEngine] Patch failed. Content not found (tried strict, trimmed, and flex-indent).\nSearch Block Preview:\n${cleanSearch.slice(0, 100)}...`);
        return null;
      };

      // NEW: Batch Simulator that holds virtual state across multiple plans
      const simulateBatchExecution = async (plans) => {
        const virtualFileMap = new Map(); // path -> content
        const planResults = [];

        log(`Batch Simulating ${plans.length} plans...`, 'purple');

        for (const plan of plans) {
          const actions = plan.ops || [];
          const changes = [];

          for (const act of actions) {
            let originalContent = "";
            let newContent = "";
            let isNew = false;

            try {
              // Resolve Original Content
              if (virtualFileMap.has(act.path)) {
                const v = virtualFileMap.get(act.path);
                if (v === null) {
                  // Was deleted earlier in this batch
                  if (act.type === 'delete') {
                    // Idempotency: already deleted
                    changes.push({
                      path: act.path,
                      type: 'DELETE',
                      originalContent: "",
                      newContent: "",
                      isNew: false
                    });
                    continue;
                  }
                  if (act.type.toLowerCase() === 'patch') {
                    log(`Batch Sim Warning (${plan.id}): Patch skipped (file deleted earlier) ${act.path}`, 'warn');
                    continue;
                  }
                  // WRITE recreates it
                  isNew = true;
                  originalContent = "";
                } else {
                  originalContent = v;
                }
              } else {
                try {
                  originalContent = await Deck0.vfs.read(act.path);
                } catch (e) {
                  if (act.type === 'write') {
                    isNew = true;
                    originalContent = "";
                  } else if (act.type === 'delete') {
                    // Idempotency: delete missing file is a no-op success
                    changes.push({
                      path: act.path,
                      type: 'DELETE',
                      originalContent: "",
                      newContent: "",
                      isNew: false
                    });
                    virtualFileMap.set(act.path, null);
                    continue;
                  } else {
                    // PATCH on a missing file is almost always hallucination; skip instead of aborting the whole batch
                    log(`Batch Sim Warning (${plan.id}): Patch skipped (file missing) ${act.path}`, 'warn');
                    continue;
                  }
                }
              }

              // Compute New Content
              if (act.type === 'delete') {
                newContent = null;
              } else if (act.type === 'write') {
                newContent = act.content;
              } else if (act.type.toLowerCase() === 'patch') {
                // V17.0 Update: Pass the whole action object to corePatch
                const patched = corePatch(originalContent, act);
                if (patched === null) {
                  // Don't abort the entire batch on a single failed patch (often a context drift / hallucination).
                  // Keep simulating the rest so the user can still execute partial successes.
                  log(`Batch Sim Warning (${plan.id}): Patch failed (skipped) ${act.path}`, 'warn');
                  continue;
                }
                newContent = patched;
              }

              // Update Virtual Map
              virtualFileMap.set(act.path, newContent);

              changes.push({
                path: act.path,
                type: act.type.toUpperCase(),
                originalContent: originalContent,
                newContent: newContent === null ? "" : newContent,
                isNew: isNew
              });

            } catch (e) {
              log(`Batch Critical Error: ${e.message}`, 'error');
              return null;
            }
          }

          if (changes.length > 0) {
            planResults.push({
              plan: plan,
              changes: changes
            });
          }
        }

        return planResults;
      };

      const applyXmlPatch = async (path, patchXml) => {
        return 0;
      }; // Deprecated XML stub

      const executeChanges = async () => {
        setBadgeState('EXECUTING', THEME.success);
        switchTermTab('logs');

        if (!store.getState().protocol.plans.length) return log("No plans detected.", 'warn');

        log("Resolving Execution Artifacts...", 'info');
        let rawText = Deck0.store.getState().protocol.lastAnswerMarkdown;
        if (!rawText || rawText.length < 10) {
          log("Network Cache empty. Fetching from DOM...", 'warn');
          rawText = await getGeminiRawTextFallback();
        } else {
          log("Using Network Cache (Clean).", 'success');
        }

        if (!rawText) {
          log("Could not retrieve output.", 'error');
          setBadgeState('READY', THEME.success);
          return;
        }

        // 1. Run Batch Simulation
        const planGroups = await simulateBatchExecution(store.getState().protocol.plans);

        if (!planGroups) {
          log("Batch Simulation Failed. Execution aborted.", 'error');
          setBadgeState('READY', THEME.success);
          return;
        }

        if (planGroups.length === 0) {
          log("No executable operations found in text.", 'warn');
          setBadgeState('READY', THEME.success);
          return;
        }

        // 2. Execute Writes (no preflight modal; review happens in-editor like Dev0)
        log("Applying changes from simulation...", 'purple');
        let totalW = 0,
          totalD = 0;

        for (const group of planGroups) {
          updatePlanStatus(group.plan.id, 'running');
          let pW = 0,
            pD = 0;

          for (const change of group.changes) {
            if (change.type === 'DELETE') {
              if (await Executor.delete(change.path)) {
                pD++;
                totalD++;
              }
            } else {
              if (await Executor.write(change.path, change.newContent)) {
                pW++;
                totalW++;
                if (change.isNew) Deck0Actions.session.addCreatedFile(change.path);
              }
            }
          }

          if (!Deck0.store.getState().session.executionLog.some(c => c.id === group.plan.id)) {
            const changedFiles = group.changes.map(c => c.path);
            Deck0Actions.session.addExecutionLogItem({
              id: group.plan.id,
              title: group.plan.title,
              desc: group.plan.desc,
              files: changedFiles
            });
          }
          updatePlanStatus(group.plan.id, 'done');
          setPlanMarker(group.plan.id, 'success');
        }

        renderExecutionLog();
        log(`Batch Done: Write:${totalW} Del:${totalD}`, 'success');
        await refreshFileTree();
        renderChangedFiles(); // [V17.9] Update File List
        setBadgeState('READY', THEME.success);
      };

      const executeSinglePlan = async (planId) => {
        setBadgeState('EXECUTING', THEME.success);
        let rawText = Deck0.store.getState().protocol.lastAnswerMarkdown;
        if (!rawText || rawText.length < 10) rawText = await getGeminiRawTextFallback();
        if (!rawText) {
          log("No source text.", 'error');
          setBadgeState('READY', THEME.success);
          return;
        }

        // V17: Pass single plan to batch simulator for consistent logic
        const plan = store.getState().protocol.plans.find(p => p.id === planId);
        const planGroups = await simulateBatchExecution([plan]);

        if (!planGroups || planGroups.length === 0) {
          log("Plan execution simulation failed.", 'error');
          setBadgeState('READY', THEME.success);
          return;
        }

        // Snapshot & Execute
        const fileChanges = planGroups[0].changes;
        const paths = fileChanges.map(c => c.path);
        log(`Smart Snapshot: protecting ${paths.length} files...`, 'purple');
        const _branchSnap = await createTargetedSnapshot(paths); Deck0.store.mutate('session', s => { s.branchSnapshot = _branchSnap; });
        updatePlanStatus(planId, 'running');

        let w = 0,
          d = 0;
        for (const change of fileChanges) {
          if (change.type === 'DELETE') {
            if (await Executor.delete(change.path)) {
              d++;
            }
          } else {
            if (await Executor.write(change.path, change.newContent)) {
              w++;
              if (change.isNew) Deck0Actions.session.addCreatedFile(change.path);
            }
          }
        }

        if (!Deck0.store.getState().session.executionLog.some(c => c.id === planId)) {
          const changedFiles = fileChanges.map(c => c.path);
          Deck0Actions.session.addExecutionLogItem({
            id: plan.id,
            title: plan.title,
            desc: plan.desc,
            files: changedFiles
          });
          renderExecutionLog();
        }

        log(`Plan Executed. (Written: ${w}, Deleted: ${d})`, 'success');
        updatePlanStatus(planId, 'done');
        setPlanMarker(planId, 'success');
        await refreshFileTree();
        renderChangedFiles(); // [V17.9] Update File List
        setBadgeState('READY', THEME.success);
      };
      const cleanupEmptyParents = async (filePath) => {
        try {
          let parts = filePath.split('/');
          parts.pop(); // Remove file

          while (parts.length > 0) {
            const dirPath = parts.join('/');
            let isEmpty = false;

            if (Deck0.store.getState().project.storageMode === 'LOCAL') {
              try {
                const dir = await getDir(Deck0.store.getState().project.dirHandle, dirPath, false);
                let entries = 0;
                for await (const _ of dir.keys()) {
                  entries++;
                  break;
                }
                if (entries === 0) {
                  // Delete empty folder (non-recursive safety)
                  await dir.removeEntry(parts[parts.length - 1]);
                  isEmpty = true;
                }
              } catch (e) {
                break;
              } // Dir might not exist or error
            } else if (Deck0.store.getState().project.storageMode === 'VIRTUAL') {
              const keys = await IDBHelper.getAllKeys();
              // Check if any key starts with dirPath + '/'
              const hasChildren = keys.some(k => k.startsWith(dirPath + '/'));
              if (!hasChildren) {
                // VFS 'folders' are virtual, but we might want to remove strict references if we had explicit folder objects (we don't in this key-value VFS).
                // However, strictly speaking, in this VFS, if no files exist, the folder disappears from the tree automatically on refresh.
                // So explicit cleanup is moot for VFS unless we had .gitkeep files.
                // If .gitkeep was deleted, the folder is gone. 
                isEmpty = true;
              }
            }

            if (isEmpty) {
              log(`🧹 Cleaned up empty folder: ${dirPath}`, 'info');
              parts.pop(); // Go up one level
            } else {
              break; // Not empty, stop traversing up
            }
          }
        } catch (e) {
          console.log('Cleanup warning:', e);
        }
      };

      const undoSinglePlan = async (planId) => {
        // [V18] ���先使用 SnapshotManager
        if (SnapshotManager.currentConversationId) {
          if (!confirm(`Rollback ${planId}?`)) return;
          const success = await SnapshotManager.undoOperation(planId);
          if (success) {
            Deck0Actions.session.removeExecutionLogItem(planId);
            renderExecutionLog();
            setPlanMarker(planId, 'reverted');
            await refreshFileTree();
            renderChangedFiles();
            return;
          }
        }

        // 回退到旧的快照方式
        const _bs = Deck0.store.getState().session.branchSnapshot; if (!_bs || _bs.length === 0) return log("No targeted snapshot available.", 'error');
        if (!confirm(`Rollback ${planId}?`)) return;

        // REMOVE LOGIC MOVED HERE: Only removes from log AFTER confirmation
        Deck0Actions.session.removeExecutionLogItem(planId);
        renderExecutionLog();

        log(`Rolling back ${planId}...`, 'purple');
        let restoredCount = 0,
          deletedCount = 0;
        for (const record of Deck0.store.getState().session.branchSnapshot) {
          if (record.status === 'modified') {
            if (await Executor.restoreWrite(record.path, record.content)) restoredCount++;
          } else if (record.status === 'created') {
            if (await Executor.restoreDelete(record.path)) {
              deletedCount++;
              await cleanupEmptyParents(record.path);
            }
          }
        }
        log(`Rollback Done. (Restored: ${restoredCount}, Deleted: ${deletedCount})`, 'success');
        setPlanMarker(planId, 'reverted');
        await refreshFileTree();
        renderChangedFiles(); // [V17.9] Update File List
      };
      const undoChanges = async () => {
        try {
          if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode) await exitEditorViewMode();
        } catch (e) { }
        // [V18] 优先使用 SnapshotManager
        if (SnapshotManager.currentConversationId) {
          if (!confirm("Undo all changes from current session?")) return;
          await SnapshotManager.undoConversation();
          Deck0Actions.session.resetPendingChanges();
          store.getState().protocol.plans.forEach(p => {
            if (p.status === 'done') setPlanMarker(p.id, 'reverted');
          });
          Deck0Actions.session.clearExecutionLog();
          renderExecutionLog();
          await refreshFileTree();
          renderChangedFiles();
          return;
        }

        // 回退到旧的快照方式
        if (!Deck0.store.getState().project.baselineSnapshot) return;
        if (!confirm("Undo all? (Rollback to Main Snapshot)")) return;
        log("Undoing (Global)...", 'warn');
        for (const f of Deck0.store.getState().project.baselineSnapshot) await Executor.restoreWrite(f.path, f.content);
        for (const p of Deck0.store.getState().session.createdFiles) await Executor.restoreDelete(p);
        log("Undo complete.", 'success');

        // [V15.1.5 FIX]: Clear created files list after global undo to avoid errors on next run
        Deck0Actions.session.resetPendingChanges();

        // Mark all plans as reverted visually
        store.getState().protocol.plans.forEach(p => {
          if (p.status === 'done') setPlanMarker(p.id, 'reverted');
        });

        Deck0Actions.session.clearExecutionLog();
        renderExecutionLog();
        await refreshFileTree();
        renderChangedFiles(); // [V17.9] Update File List
      };

      const keepFileChange = async (path) => {
        const p = normalizeVfsPath(path);
        if (!p) return;

        // Remove from pending list
        Deck0Actions.session.removeSyncPath(p);
        Deck0Actions.session.removeCreatedFile(p);

        // Remove from SnapshotManager so Undo All won't revert it
        try {
          const convId = SnapshotManager.currentConversationId;
          const conv = convId ? SnapshotManager.conversations.get(convId) : null;
          if (conv && conv.files) conv.files.delete(p);
        } catch (e) { }

        // Update baseline snapshot for this file so fallback undo won't revert it
        try {
          let exists = true;
          let content = '';
          try {
            content = await readFileContent(p);
          } catch (e) {
            exists = false;
          }

          if (!Array.isArray(Deck0.store.getState().project.baselineSnapshot)) Deck0.store.mutate('project', proj => { proj.baselineSnapshot = []; });
          const idx = Deck0.store.getState().project.baselineSnapshot.findIndex(f => normalizeVfsPath(f.path) === p);
          if (exists) {
            if (idx >= 0) Deck0.store.mutate('project', proj => { if (proj.baselineSnapshot && proj.baselineSnapshot[idx]) proj.baselineSnapshot[idx].content = content; });
            else Deck0.store.mutate('project', proj => {
              if (proj.baselineSnapshot) proj.baselineSnapshot.push({
                path: p,
                content
              });
            });
          } else {
            if (idx >= 0) Deck0.store.mutate('project', proj => { if (proj.baselineSnapshot) proj.baselineSnapshot.splice(idx, 1); });
          }
        } catch (e) { }

        renderChangedFiles();
        log(`✅ Kept: ${p}`, 'success');
      };

      const undoFileChange = async (path) => {
        const p = normalizeVfsPath(path);
        if (!p) return;
        if (!confirm(`Undo changes to ${p}?`)) return;

        let restored = false;

        // Prefer SnapshotManager if available (precise pre-op content)
        try {
          const convId = SnapshotManager.currentConversationId;
          const conv = convId ? SnapshotManager.conversations.get(convId) : null;
          const snap = conv && conv.files ? conv.files.get(p) : null;
          if (snap) {
            if (snap.existed) await Executor.restoreWrite(p, snap.content || '');
            else {
              await Executor.restoreDelete(p);
              try {
                await cleanupEmptyParents(p);
              } catch (e) { }
            }
            restored = true;
          }
        } catch (e) { }

        // Fallback: backupSnapshot
        if (!restored) {
          const rec = Array.isArray(Deck0.store.getState().project.baselineSnapshot) ? Deck0.store.getState().project.baselineSnapshot.find(f => normalizeVfsPath(f.path) === p) : null;
          if (rec) {
            await Executor.restoreWrite(p, rec.content || '');
            restored = true;
          } else {
            await Executor.restoreDelete(p);
            try {
              await cleanupEmptyParents(p);
            } catch (e) { }
            restored = true;
          }
        }

        Deck0Actions.session.removeSyncPath(p);
        Deck0Actions.session.removeCreatedFile(p);

        renderChangedFiles();
        try {
          await refreshFileTree(true);
        } catch (e) { }
        log(`↩️ Undone: ${p}`, restored ? 'success' : 'warn');
      };

      const keepAllChanges = async () => {
        try {
          if (Deck0.store.getState().legacy.DECK0_VIEW && Deck0.store.getState().legacy.DECK0_VIEW.mode) await exitEditorViewMode();
        } catch (e) { }
        const files = [...new Set(Deck0.store.getState().session.syncList)];
        let snapshotFiles = null;
        try {
          // [V18.2] KEEP snapshots must be complete for safe rollback
          snapshotFiles = await captureProjectState(true);
          Deck0.store.mutate('project', p => { p.baselineSnapshot = snapshotFiles; });
        } catch (e) {
          log(`KEEP ALL warning: snapshot baseline update failed: ${e.message}`, 'warn');
        }

        const finalizeKeep = async (snapshotTitle = null) => {
          // Always create snapshot if title is provided (User requested Keep All)
          if (snapshotTitle) {
            const created = await addTimelineSnapshot('assistant', snapshotTitle, files, snapshotFiles);
            if (created) log(`📌 Code assistant snapshot created`, 'purple');
          }

          // Clear pending lists + drop undo snapshots for this run
          Deck0Actions.session.resetPendingChanges();
          try {
            const convId = SnapshotManager.currentConversationId;
            if (convId) SnapshotManager.conversations.delete(convId);
            SnapshotManager.currentConversationId = null;
          } catch (e) { }

          renderChangedFiles();
          log("✅ Kept all changes.", 'success');
        };

        if (files.length > 0) {
          showSnapshotModal('AI Snapshot', (customTitle) => finalizeKeep(customTitle));
        } else {
          log("No pending changes to keep.", 'info');
        }
      };

      // [V17.0] Interaction Interceptor (The Bridge)
      _contextBuffer = null;
      const handledEvents = new WeakSet();

      const hijackInteractions = () => {
        // 1. Click Interceptor (Run / Send)
        document.body.addEventListener('click', async (e) => {
          // Condition: Protocol Mode + Trusted Event (User Click)
          if (Deck0.store.getState().settings.interactionMode !== 'PROTOCOL' || !e.isTrusted) return;
          if (handledEvents.has(e.target)) return;

          // Target Detection (Generic & Specific)
          const btn = e.target.closest('button');
          if (!btn) return;

          const label = (btn.getAttribute('aria-label') || '').toLowerCase();
          const isSend = label.includes('run') || label.includes('send') || btn.querySelector(DECK0_SELECTORS.SEND_ICON_PAPER_PLANE); // Common icons

          if (!isSend) return;

          // Try to find the associated textarea
          // Strategy: Look for the specific Gemini selectors first, then fallback to global active
          const textarea = Deck0Effects.dom.findPromptTextarea();

          if (!textarea) return; // Can't inject if no textarea

          const userInput = textarea.value || "";

          // HIJACK START
          e.preventDefault();
          e.stopPropagation();

          // [V17.5] Brutal Paste Strategy: Modify DOM directly
          await prepareDeck0Payload(textarea, userInput);

          // Give React/Angular a tick to digest the input event from prepareDeck0Payload
          await new Promise(r => Deck0Effects.clock.setTimeout(r, 20));

          // Re-dispatch
          const newEvt = new MouseEvent('click', {
            bubbles: true,
            cancelable: true,
            view: window
          });
          handledEvents.add(btn);

          btn.dispatchEvent(newEvt);
        }, true); // Capture phase

        // 2. Keydown Interceptor (Enter on Textarea)
        document.body.addEventListener('keydown', async (e) => {
          if (Deck0.store.getState().settings.interactionMode !== 'PROTOCOL' || !e.isTrusted) return;
          if (e.key !== 'Enter') return;

          const textarea = e.target.closest('textarea');
          if (!textarea) return;
          if (!textarea.closest(DECK0_SELECTORS.PROMPT_WRAPPER) && !textarea.closest(DECK0_SELECTORS.PROMPT_FOOTER)) return;

          // CASE A: Cmd/Ctrl + Enter => Automation SEND
          if (e.metaKey || e.ctrlKey) {
            e.preventDefault();
            e.stopPropagation();

            const userInput = textarea.value || "";
            await prepareDeck0Payload(textarea, userInput);

            await new Promise(r => Deck0Effects.clock.setTimeout(r, 20));

            // Re-dispatch with modifiers to trigger Send
            const newEvt = new KeyboardEvent('keydown', {
              key: 'Enter',
              code: 'Enter',
              keyCode: 13,
              which: 13,
              bubbles: true,
              cancelable: true,
              view: window,
              ctrlKey: e.ctrlKey,
              metaKey: e.metaKey,
              shiftKey: e.shiftKey,
              altKey: e.altKey
            });
            textarea.dispatchEvent(newEvt);
            return;
          }

          // CASE B: Plain Enter (No Shift) => Forced NEWLINE
          // (Prevent default "Send" behavior of the app, enforce multiline editing)
          if (!e.shiftKey) {
            e.preventDefault();
            e.stopPropagation();

            const start = textarea.selectionStart;
            const end = textarea.selectionEnd;
            const val = textarea.value;

            // Insert Newline
            textarea.value = val.substring(0, start) + "\n" + val.substring(end);
            textarea.selectionStart = textarea.selectionEnd = start + 1;

            // Notify Framework (Angular/React) & Scroll
            textarea.dispatchEvent(new Event('input', {
              bubbles: true
            }));
            textarea.scrollTop = textarea.scrollHeight;
            return;
          }

          // CASE C: Shift+Enter => Allow Bubbling (Standard Newline)
        }, true);
      };

      const prepareDeck0Payload = async (textareaEl, userText) => {
        const fileSelectionMap = Deck0.store.getState().ui.fileSelectionMap;
        Deck0Actions.session.setLastUserInstruction(userText);
        Deck0Actions.runtime.beginRun();

        // 2. Capture Context
        try {
          // [V18.2 Best Practice] Capture FULL Project State for Safety (Ignored files are safe-guarded)
          const _snap2 = await captureProjectState(true); Deck0.store.mutate('project', p => { p.baselineSnapshot = _snap2; });

          // Filter for AI Context (Token Optimization) based on User Selection
          const contextFiles = Deck0.store.getState().project.baselineSnapshot.filter(f => {
            if (fileSelectionMap.has(f.path) && !fileSelectionMap.get(f.path).checked) return false;
            return true;
          });

          // New run: reset pending changes to be per-run (Cline-like)
          Deck0Actions.session.resetPendingChanges();
          try {
            const convId = SnapshotManager.currentConversationId;
            if (convId) SnapshotManager.conversations.delete(convId);
            SnapshotManager.currentConversationId = null;
          } catch (e) { }

          let p = `[PROJECT CONTEXT - ${contextFiles.length} FILES]\n`;
          p += formatFileTree(contextFiles);
          contextFiles.forEach(f => {
            p += `### File: ${f.path}\n\`\`\`${f.path.split('.').pop()}\n${f.content}\n\`\`\`\n`;
          });

          p += `--------------------------------------------------\n[USER INSTRUCTIONS]\n${userText}\n\n`;

          // [V17.5] DOM Injection Strategy (Brutal Paste)
          // We modify the textarea directly so the browser sends the full context naturally.
          // This bypasses the XHR Context Injection logic (avoiding double inject).
          if (textareaEl) {
            textareaEl.value = p;
            textareaEl.dispatchEvent(new Event('input', {
              bubbles: true
            }));
          }

          _contextBuffer = null; // EXPLICITLY NULL to disable XHR context injection

          // 3. Reset Protocol State
          Deck0Actions.protocol.resetForNewRun();

          // [V17.1 Fix] Clear Accordions & Reset Counts
          const tasksBody = Deck0.ui.refs.tasksBody;
          if (tasksBody) {
            tasksBody.replaceChildren();
            const th = tasksBody.previousElementSibling;
            if (tasksCount) tasksCount.textContent = 'TASKS (0)';
          }
          const filesBody = Deck0.ui.refs.filesBody;
          if (filesBody) {
            filesBody.replaceChildren();
            const fh = filesBody.previousElementSibling;
            if (filesCount) filesCount.textContent = 'CHANGED FILES (0)';
          }

          // 4. Switch UI
          // [V18.3] Clear Chat Logic
          if (Deck0.store.getState().settings.clearChat && chatHistory) {
            chatHistory.replaceChildren();
          }
          addChatMessage('user', userText); // Add to Chat UI

          // [P6-SYNC] Sync to Shadow UI if enabled
          if (Deck0.ui.shadow?.enabled && Deck0.ui.shadow.addChatMessage) {
            Deck0.ui.shadow.addChatMessage('user', userText);
          }
          // switchTermTab('stream'); // [MOD] Disabled auto-switch
          Deck0.ui.refs.streamView.textContent = "";

          if (updateSendBtnState) updateSendBtnState(true);
          setBadgeState('THINKING', THEME.purple);

        } catch (e) {
          log(`Context Capture Failed: ${e.message}`, 'error');
          _contextBuffer = null;
        }
      };

      // Elevate to Window for Global access
      // [TP-P5-1 Batch 5] API Migration
      Orchestrator.mount = mountProject;
      Orchestrator.run = runPipeline;

      // ===================== DECK0 KERNEL (S1) =====================
      // Single entrypoint. Keep behavior identical; only centralize boot order + module visibility.
      const Deck0Kernel = (() => {
        let coreRef = null;
        const getCore = () => {
          if (coreRef) return coreRef;
          coreRef = {
            path: {
              normalizeVfsPath,
              sanitizeOpPath,
              isSafeVfsPath,
              isTemplatePlaceholderPath
            },
            protocol: {
              parseActions
            },
            patch: {
              corePatch
            },
            diff: DiffEngine,
            highlight: TitanHighlighter,
            util: Deck0CoreUtils
          };
          return coreRef;
        };

        let constantsRef = null;
        const getConstants = () => {
          if (constantsRef) return constantsRef;
          constantsRef = {
            storageKeys: DECK0_STORAGE_KEYS,
            domIds: DECK0_DOM_IDS,
            selectors: DECK0_SELECTORS,
            netHints: DECK0_NET_HINTS,
            timelineKey: DECK0_TIMELINE_KEY,
            appId: APP_ID
          };
          return constantsRef;
        };

        let testsRef = null;
        const getTests = () => {
          if (testsRef) return testsRef;

          const make = () => {
            const run = () => {
              const rows = [];
              const push = (name, ok, detail = '') => rows.push({ test: name, ok: ok ? 'PASS' : 'FAIL', detail });

              const safe = (name, fn) => {
                try { fn(); push(name, true); } catch (e) { push(name, false, e && e.message ? e.message : String(e)); }
              };

              const core = getCore();
              safe('core.path.normalizeVfsPath trims backticks', () => {
                const out = core.path.normalizeVfsPath('`a/b`');
                if (out !== 'a/b') throw new Error(`got ${String(out)}`);
              });
              safe('core.path.sanitizeOpPath strips :line', () => {
                const out = core.path.sanitizeOpPath('src/app.ts:12:5');
                if (out !== 'src/app.ts') throw new Error(`got ${String(out)}`);
              });
              safe('core.path.isSafeVfsPath blocks traversal', () => {
                if (core.path.isSafeVfsPath('../x')) throw new Error('expected false');
              });
              safe('core.path.isSafeVfsPath allows repo-relative', () => {
                if (!core.path.isSafeVfsPath('src/app.ts')) throw new Error('expected true');
              });
              safe('core.protocol.parseActions parses write+delete', () => {
                const input = [
                  '### DECK0_OP: [WRITE] a.txt',
                  '```txt',
                  'hello',
                  '',
                  '```',
                  '### DECK0_OP: [DELETE] b.txt'
                ].join('\n');
                const acts = core.protocol.parseActions(input);
                if (!Array.isArray(acts) || acts.length !== 2) throw new Error(`expected 2 actions, got ${acts ? acts.length : 'null'}`);
                if (acts[0].type !== 'write' || acts[0].path !== 'a.txt') throw new Error('write mismatch');
                if (acts[1].type !== 'delete' || acts[1].path !== 'b.txt') throw new Error('delete mismatch');
              });
              safe('core.patch.corePatch replaces searchBlock', () => {
                const out = core.patch.corePatch('a\nb\n', { searchBlock: 'b', replaceBlock: 'c' });
                if (out !== 'a\nc\n') throw new Error('patch failed');
              });
              safe('core.patch.corePatch idempotent when already applied', () => {
                const out = core.patch.corePatch('a\nc\n', { searchBlock: 'b', replaceBlock: 'c' });
                if (out == null) throw new Error('expected non-null');
              });

              // [TP-P0-CORE-TESTS] 新增：util 纯函数测试
              safe('core.util.escapeRegExp escapes special chars', () => {
                const out = core.util.escapeRegExp('a.b*c?d');
                if (out !== 'a\\.b\\*c\\?d') throw new Error(`got ${out}`);
              });
              safe('core.util.getExtension returns extension', () => {
                if (core.util.getExtension('a/b.txt') !== 'txt') throw new Error('expected txt');
                if (core.util.getExtension('no-ext') !== '') throw new Error('expected empty');
                if (core.util.getExtension('.gitignore') !== 'gitignore') throw new Error('expected gitignore');
              });
              safe('core.util.getFileName returns filename', () => {
                if (core.util.getFileName('a/b/c.txt') !== 'c.txt') throw new Error('expected c.txt');
                if (core.util.getFileName('solo.js') !== 'solo.js') throw new Error('expected solo.js');
              });
              safe('core.util.getParentPath returns parent', () => {
                if (core.util.getParentPath('a/b/c.txt') !== 'a/b') throw new Error('expected a/b');
                if (core.util.getParentPath('solo.js') !== '') throw new Error('expected empty');
              });
              safe('core.util.countLines counts correctly', () => {
                if (core.util.countLines('a\nb\nc') !== 3) throw new Error('expected 3');
                if (core.util.countLines('') !== 0) throw new Error('expected 0 for empty');
              });
              safe('core.util.truncateLines truncates', () => {
                const input = 'a\nb\nc\nd\ne';
                const out = core.util.truncateLines(input, 3, '...');
                if (out !== 'a\nb\nc...') throw new Error(`got ${out}`);
              });

              try { console.table(rows); } catch (e) { console.log(rows); }
              return rows;
            };
            return { run };
          };

          testsRef = make();
          return testsRef;
        };

        const expose = () => {
          const deck0 = window.Deck0 || (window.Deck0 = {});
          deck0.kernel = deck0.kernel || {};

          deck0.kernel.boot = boot;
          deck0.kernel.modules = modules;

          // Minimal, stable references (debug-friendly; not part of legacy UI contract)
          deck0.constants = getConstants();
          deck0.constants.theme = THEME;
          deck0.debug = DECK0_DEBUG;
          deck0.core = getCore();
          deck0.tests = getTests();
          deck0.store = store;
          deck0.actions = Deck0Actions;
          deck0.effects = Deck0Effects;
          deck0.vfs = VfsManager;
          deck0.executor = Executor;
          deck0.git = GitLink; // [TP-P3-4] Expose GitLink
          deck0.transport = Transport;
          deck0.provider = GeminiProvider;
          deck0.protocolEngine = ProtocolEngine;
          deck0.orchestrator = Orchestrator;
          // [TP-P5-0] F12 Debug Tools Export (Lazy)
          deck0.f12 = deck0.f12 || {};

          // [SOP-4.3] Explicit Closure Bridging
          deck0.getBaselineForPath = getBaselineForPath;
          deck0.readFileContent = readFileContent;
          deck0.keepAllChanges = keepAllChanges;
          deck0.undoChanges = undoChanges;
          deck0.keepFileChange = keepFileChange;
          deck0.undoFileChange = undoFileChange;
          deck0.enterDiffReviewMode = enterDiffReviewMode;
          deck0.executeSinglePlan = executeSinglePlan;
          deck0.undoSinglePlan = undoSinglePlan;
          deck0.executeChanges = executeChanges;

          // Structured namespaces (aliases only; for readability & secondary dev tooling)
          // [TP-P3-0] Hot-Upgrade Safe: Always rebind fields (no `|| { store }` pattern)
          deck0.state = deck0.state || {};
          deck0.state.store = store;

          deck0.storage = deck0.storage || {};
          deck0.storage.vfs = VfsManager;
          deck0.storage.commitDriver = CommitDriver;

          deck0.protocol = deck0.protocol || {};
          deck0.protocol.engine = ProtocolEngine;
          deck0.protocol.transport = Transport;
          deck0.protocol.provider = GeminiProvider;
          deck0.protocol.domOnlyProvider = DomOnlyProvider;

          deck0.ui = deck0.ui || {};
          deck0.ui.adapter = BridgeUIAdapter;
          deck0.ui.refs = Deck0UIRefs;
          deck0.ui.lifecycle = Deck0UI; // [TP-P4-1] Expose UI init lifecycle

          // [TP-P3-1] UI 官方导出（只做导出，不搬逻辑）
          deck0.ui.el = el;
          deck0.ui.log = log;
          deck0.ui.tree = deck0.ui.tree || {};
          deck0.ui.tree.refresh = refreshFileTree;
          deck0.ui.files = deck0.ui.files || {};
          deck0.ui.files.render = renderChangedFiles;
          deck0.ui.status = deck0.ui.status || {};
          deck0.ui.status.set = setBadgeState;

          // [TP-P2] Module Homing
          deck0.ui.editor = EditorManager;
          deck0.timeline = deck0.timeline || {};
          deck0.timeline.db = TimelineDB;
          deck0.git = GitLink;
          if (deck0.executor) deck0.executor.snapshot = SnapshotManager;

          // ===================== P6 SHADOW UI =====================
          // [P6-L1] Shadow UI Parallel Rendering System
          deck0.ui.shadow = deck0.ui.shadow || {};
          deck0.ui.shadow.enabled = false;
          deck0.ui.shadow.root = null;
          deck0.ui.shadow.refs = {};
          deck0.ui.shadow.components = {};
          deck0.ui.shadow.logic = {}; // [P6-LOGIC] Init logic namespace
          deck0.ui.shadow.adapter = ShadowBridgeUIAdapter; // [P6-BEHAVIOR] Mount adapter

          // [P6-ICONS] Icon Registry (SVG path data)
          const SHADOW_ICONS = {
            retry: 'M17.65 6.35C16.2 4.9 14.21 4 12 4c-4.42 0-7.99 3.58-7.99 8s3.57 8 7.99 8c3.73 0 6.84-2.55 7.73-6h-2.08c-.82 2.33-3.04 4-5.65 4-3.31 0-6-2.69-6-6s2.69-6 6-6c1.66 0 3.14.69 4.22 1.78L13 11h7V4l-2.35 2.35z',
            thumbsUp: 'M1 21h4V9H1v12zm22-11c0-1.1-.9-2-2-2h-6.31l.95-4.57.03-.32c0-.41-.17-.79-.44-1.06L14.17 1 7.59 7.59C7.22 7.95 7 8.45 7 9v10c0 1.1.9 2 2 2h9c.83 0 1.54-.5 1.84-1.22l3.02-7.05c.09-.23.14-.47.14-.73v-1.91l-.01-.01L23 10z',
            thumbsDown: 'M15 3H6c-.83 0-1.54.5-1.84 1.22l-3.02 7.05c-.09.23-.14.47-.14.73v1.91l.01.01L1 14c0 1.1.9 2 2 2h6.31l-.95 4.57-.03.32c0 .41.17.79.44 1.06L9.83 23l6.59-6.59c.36-.36.58-.86.58-1.41V5c0-1.1-.9-2-2-2zm4 0v12h4V3h-4z',
            gear: 'M9.405 1.05c-.413-1.4-2.397-1.4-2.81 0l-.1.34a1.464 1.464 0 0 1-2.105.872l-.31-.17c-1.283-.698-2.686.705-1.987 1.987l.169.311c.446.82.023 1.841-.872 2.105l-.34.1c-1.4.413-1.4 2.397 0 2.81l.34.1a1.464 1.464 0 0 1 .872 2.105l-.17.31c-.698 1.283.705 2.686 1.987 1.987l.311-.169a1.464 1.464 0 0 1 2.105.872l.1.34c.413 1.4 2.397 1.4 2.81 0l.1-.34a1.464 1.464 0 0 1 2.105-.872l.31.17c1.283.698 2.686-.705 1.987-1.987l-.169-.311a1.464 1.464 0 0 1 .872-2.105l.34-.1c1.4-.413 1.4-2.397 0-2.81l-.34-.1a1.464 1.464 0 0 1-.872-2.105l.17-.31c-.698-1.283-.705-2.686-1.987-1.987l-.311.169a1.464 1.464 0 0 1-2.105-.872l-.1-.34zM8 10.93a2.929 2.929 0 1 1 0-5.86 2.929 2.929 0 0 1 0 5.858z',
            file: 'M13 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V9z',
            task: 'M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z'
          };

          // [P6-ICONS] Icon Factory Function
          const Icon = (name, viewBox = '0 0 24 24') => {
            const path = SHADOW_ICONS[name];
            if (!path) return null;
            const svg = el('svg', {
              cls: 'deck0-icon',
              attrs: { viewBox, fill: 'currentColor' }
            });
            svg.appendChild(el('path', { attrs: { d: path } }));
            return svg;
          };

          // [P6-L1-2] Shadow Root Container
          // [P6-BEHAVIOR] Task List Renderer (Pixel-Perfect Logic)

          // [P6-BEHAVIOR] Flyout Helpers
          let shadowFlyoutTimer = null;

          deck0.ui.shadow.hideFlyout = () => {
            if (shadowFlyoutTimer) clearTimeout(shadowFlyoutTimer);
            shadowFlyoutTimer = setTimeout(() => {
              const flyout = deck0.ui.shadow.refs.flyout;
              if (flyout) flyout.classList.remove('visible');
            }, 200);
          };

          deck0.ui.shadow.showFlyout = (planId, targetEl) => {
            if (shadowFlyoutTimer) clearTimeout(shadowFlyoutTimer);

            const flyout = deck0.ui.shadow.refs.flyout;
            if (!flyout) return;

            const plan = Deck0.store.getState().protocol.plans.find(p => p.id === planId);
            if (!plan) return;

            const details = { ...plan, files: [] };
            if (plan.ops) {
              const fileMap = new Map();
              plan.ops.forEach(act => {
                if (!fileMap.has(act.path)) fileMap.set(act.path, { path: act.path, type: act.type, hunks: 0 });
                if (act.type === 'patch') fileMap.get(act.path).hunks++;
              });
              details.files = Array.from(fileMap.values());
            }

            flyout.replaceChildren();

            const prefix = details.id.split('-')[0];
            const tagStyle = TAG_COLORS[prefix] || { bg: 'rgba(255,255,255,0.1)', text: '#fff' };

            const titleRow = el('div', { cls: 'flyout-title', parent: flyout });
            el('span', { cls: 'deck0-tag', text: details.id, style: { background: tagStyle.bg, color: tagStyle.text }, parent: titleRow });
            el('span', { text: details.title, parent: titleRow });

            el('div', { cls: 'flyout-desc', text: details.desc, parent: flyout });

            if (details.files.length > 0) {
              el('div', { cls: 'flyout-section-title', text: `Files Affected (${details.files.length})`, parent: flyout });
              const fileList = el('div', { cls: 'flyout-file-list', parent: flyout });
              details.files.forEach(f => {
                const icon = f.type === 'write' ? '✨' : (f.type === 'delete' ? '🔴' : '📝');
                const meta = f.type === 'patch' && f.hunks > 0 ? ` (${f.hunks} hunks)` : '';
                el('div', { cls: 'flyout-file-item', text: `${icon} ${f.path}${meta}`, parent: fileList });
              });
            }

            const rect = targetEl.getBoundingClientRect();
            const flyoutLeft = rect.left - 420;
            flyout.style.left = `${flyoutLeft}px`;
            flyout.style.top = `${rect.top}px`;

            flyout.classList.add('visible');
          };

          // [P6-LOGIC] Input Data Prep
          deck0.ui.shadow.logic.prepareInputData = () => {
            const state = Deck0.store.getState();
            if (!state) return null;

            const settings = state.settings || {};
            const expertId = settings.expertId || 'ARCHITECT';
            const expert = EXPERTS[expertId] || EXPERTS['ARCHITECT'];

            return {
              expertId,
              expertName: expert.name,
              expertColor: expert.color,
              expertDesc: expert.desc,
              model: settings.model || 'gemini-3-pro-preview',
              isRunning: state.runtime?.isPipelineRunning || false,
              experts: Object.values(EXPERTS),
              models: ['gemini-3-pro-preview', 'gemini-3-flash-preview']
            };
          };

          // [P6-LOGIC] TaskList Data Prep
          deck0.ui.shadow.logic.prepareTaskListData = () => {
            const state = Deck0.store.getState();
            const plans = state.protocol.plans || [];
            const total = plans.length;
            const done = plans.filter(p => p.status === 'done').length;

            const firstIncomplete = plans.findIndex(p => p.status !== 'done');
            const currentIndex = firstIncomplete >= 0 ? firstIncomplete + 1 : total;
            const currentPlan = firstIncomplete >= 0 ? plans[firstIncomplete] : null;

            const percent = total > 0 ? Math.round((done / total) * 100) : 0;
            const hasRunning = plans.some(p => p.status === 'running');
            const isAllDone = total > 0 && done === total;

            let currentText = 'No tasks.';
            if (total > 0) {
              currentText = isAllDone ? 'All tasks completed!' : (currentPlan ? (currentPlan.title || currentPlan.id) : 'In progress...');
            }

            return {
              plans,
              summary: { total, done, currentIndex, currentText, percent, hasRunning, isAllDone }
            };
          };

          // [P6-COMPONENT] Micro-Components for TaskList
          deck0.ui.shadow.components.ProgressCard = (summary) => {
            const { total, currentIndex, done, currentText, percent, hasRunning, isAllDone } = summary;

            const Badge = el('span', {
              cls: `deck0-tasks-progress-badge ${isAllDone ? 'deck0-badge-done' : ''}`,
              text: total > 0 ? `${currentIndex}/${total}` : '0/0'
            });

            const RightIcon = hasRunning
              ? el('div', { cls: 'deck0-spinner' })
              : (isAllDone ? el('span', { cls: 'deck0-tasks-progress-check', text: '✔' }) : null);

            const isBarVisible = !(percent === 0 || percent === 100);

            return el('div', {
              cls: 'deck0-tasks-progress',
              children: [
                el('div', {
                  cls: 'deck0-tasks-progress-row',
                  children: [
                    el('div', {
                      cls: 'deck0-tasks-progress-left',
                      children: [Badge, el('div', { cls: 'deck0-tasks-progress-text', text: currentText })]
                    }),
                    el('div', {
                      cls: 'deck0-tasks-progress-right',
                      children: [
                        el('span', { cls: 'deck0-tasks-progress-count', text: total > 0 ? `${done}/${total}` : '0/0' }),
                        RightIcon
                      ]
                    })
                  ]
                }),
                el('div', {
                  cls: `deck0-tasks-progress-underline ${isBarVisible ? 'deck0-progress-visible' : 'deck0-progress-hidden'}`,
                  attrs: { style: `width: ${percent}%` }
                })
              ]
            });
          };

          deck0.ui.shadow.components.TaskRow = (plan, actions) => {
            const prefix = (plan.id.split('-')[0] || '').toUpperCase();
            // Map prefix to class
            const tagClass = ({
              'INFRA': 'deck0-tag-infra', 'CORE': 'deck0-tag-core',
              'API': 'deck0-tag-api', 'UI': 'deck0-tag-ui', 'TEST': 'deck0-tag-test'
            }[prefix]) || 'deck0-tag-default';

            // Title logic (Type coloring)
            const titleChildren = [];
            const titleMatch = plan.title.match(/^([a-z]+)(\(.*\))?:(.*)$/i);

            if (titleMatch) {
              const type = titleMatch[1].toLowerCase();
              const rest = titleMatch[3].trim();
              const typeClass = ({
                feat: 'deck0-type-feat', fix: 'deck0-type-fix',
                refactor: 'deck0-type-refactor', chore: 'deck0-type-chore'
              }[type]) || 'deck0-type-default';

              titleChildren.push(el('span', { text: `${type}:`, cls: typeClass }));
              titleChildren.push(document.createTextNode(rest));
            } else {
              titleChildren.push(document.createTextNode(plan.title));
            }

            return el('div', {
              cls: 'deck0-task-row',
              id: `shadow-plan-${plan.id}`,
              mouseenter: (e) => actions.onHover(e.currentTarget),
              mouseleave: (e) => actions.onLeave(),
              children: [
                el('div', { cls: `deck0-task-status-dot ${plan.status || 'pending'}` }),
                el('div', { cls: `deck0-task-id ${tagClass}`, text: plan.id }),
                el('div', { cls: 'deck0-task-title', children: titleChildren }),
                el('div', {
                  cls: 'deck0-task-actions',
                  children: [
                    el('div', {
                      cls: 'deck0-btn-mini run', text: '▶', attrs: { title: 'Execute' },
                      click: (e) => { e.stopPropagation(); actions.onRun(plan.id); }
                    }),
                    el('div', {
                      cls: 'deck0-btn-mini undo', text: '↺', attrs: { title: 'Undo' },
                      click: (e) => { e.stopPropagation(); actions.onUndo(plan.id); }
                    })
                  ]
                }),
                el('div', { cls: 'deck0-plan-marker' })
              ]
            });
          };

          // [P6-BEHAVIOR] Behavior Layer
          deck0.ui.shadow.renderTaskList = () => {
            const list = deck0.ui.shadow.refs.taskList;
            if (!list) return;

            // 1. Logic
            const data = deck0.ui.shadow.logic.prepareTaskListData();

            // 2. Empty State Handling
            if (data.summary.total === 0) {
              // Collapse if empty
              if (list.parentElement && list.parentElement.classList.contains('open')) {
                list.parentElement.classList.remove('open');
              }
              if (deck0.ui.shadow.refs.tasksHeaderCount) {
                deck0.ui.shadow.refs.tasksHeaderCount.textContent = 'TASKS (0)';
              }
              list.replaceChildren();
              return;
            }

            // 3. Update Header
            if (deck0.ui.shadow.refs.tasksHeaderCount) {
              deck0.ui.shadow.refs.tasksHeaderCount.textContent = `TASKS (${data.summary.done}/${data.summary.total})`;
            }

            // 4. Auto-Expand
            if (list.parentElement && !list.parentElement.classList.contains('open')) {
              list.parentElement.classList.add('open');
            }

            // 5. Render Components
            list.replaceChildren();

            // Progress Card
            list.appendChild(deck0.ui.shadow.components.ProgressCard(data.summary));

            // Task Rows
            data.plans.forEach(plan => {
              const actions = {
                onRun: (id) => Deck0.executeSinglePlan(id),
                onUndo: (id) => Deck0.undoSinglePlan(id),
                onHover: (el) => deck0.ui.shadow.showFlyout(plan.id, el),
                onLeave: () => deck0.ui.shadow.hideFlyout()
              };
              list.appendChild(deck0.ui.shadow.components.TaskRow(plan, actions));
            });
          };
          deck0.ui.shadow.mount = () => {
            if (deck0.ui.shadow.root) return deck0.ui.shadow.root;
            const appRoot = document.getElementById(APP_ID);
            if (!appRoot) return null;

            const shadowRoot = el('div', {
              id: 'deck0-shadow-root',
              attrs: { 'data-shadow': '1' },
              style: {
                display: 'none',
                pointerEvents: 'auto',
                position: 'absolute',
                top: '0', left: '0', right: '0', bottom: '0',
                zIndex: '9999'
              },
              parent: appRoot
            });

            // [P6-CSS] Inject Shadow-specific CSS
            // 1. Dynamic Expert Themes
            const expertThemes = Object.values(EXPERTS).map(e => `
              .deck0-theme-${e.id} .deck0-shadow-textarea { border: 1px solid ${e.color} !important; }
              .deck0-theme-${e.id} .deck0-shadow-badge { color: ${e.color} !important; border-color: ${e.color} !important; }
              .deck0-theme-${e.id} .deck0-shadow-send-btn { color: ${e.color} !important; }
              .deck0-theme-${e.id} .deck0-shadow-menu-item.active { border-left-color: ${e.color} !important; color: #fff; background: rgba(255,255,255,0.08); }
              .deck0-dot-${e.id} { background-color: ${e.color} !important; }
            `).join('');

            const shadowCSS = `
              ${expertThemes}
              
              /* Shadow Structure overrides */
              .deck0-shadow-main { display: flex; flex-direction: column; overflow: hidden; }
              .deck0-right { display: flex !important; flex-direction: column !important; overflow: hidden !important; }
              
              /* Shadow UI Bubble Styles */
              .deck0-panel-header { font-weight: bold; padding: 5px 12px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid rgba(255, 255, 255, 0.1); background: transparent; flex-shrink: 0; }
              .deck0-bubble { border-radius: 8px; padding: 8px 12px; max-width: 90%; font-size: 12px; color: #eee; white-space: pre-wrap; word-break: break-word; overflow-wrap: anywhere; line-height: 1.65; }
              .deck0-bubble-user { align-self: flex-end; background: rgba(10, 132, 255, 0.2); border: 1px solid rgba(10, 132, 255, 0.3); }
              .deck0-bubble-assistant { align-self: flex-start; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.1); }
              .deck0-shadow-stream-view { flex: 1; min-height: 0; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 24px; }
              .deck0-icon { width: 14px; height: 14px; }
              /* Shadow UI Sidebar Sections */
              .deck0-section-header { font-weight: bold; padding: 5px 12px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid rgba(255, 255, 255, 0.1); font-size: 11px; color: #ccc; text-transform: uppercase; letter-spacing: 0.5px; background: rgba(0,0,0,0.1); }
              .deck0-section-content { display: flex; flex-direction: column; }
              .deck0-file-row { display: flex; align-items: center; padding: 6px 12px; font-size: 12px; color: #eee; gap: 8px; cursor: pointer; transition: background 0.1s; border-bottom: 1px solid rgba(255, 255, 255, 0.03); }
              .deck0-file-row:hover { background: rgba(255, 255, 255, 0.05); }
              .deck0-task-row { padding: 8px 12px; font-size: 12px; color: #bbb; border-bottom: 1px solid rgba(255, 255, 255, 0.05); line-height: 1.4; }
              .deck0-empty-state { padding: 16px; text-align: center; color: #666; font-style: italic; font-size: 12px; }
              
              /* Shadow UI Input Component (Strict Class-Based) */
              .deck0-shadow-input-container { flex-shrink: 0; padding: 10px 10px 10px 10px; border-top: 1px solid rgba(255,255,255,0.05); background: rgba(0,0,0,0.2); position: relative; display: flex; flex-direction: column; }
              
              .deck0-shadow-textarea {
                width: 100%; 
                min-height: 100px; 
                max-height: 290px; 
                background: rgba(0,0,0,0.2); 
                border: 1px solid rgba(255,255,255,0.1); 
                border-radius: 6px; 
                padding: 8px 8px 30px 8px; /* Tightened padding */
                outline: none; 
                color: #eee; 
                font-family: "SF Mono", monospace; 
                font-size: 12px !important;
                line-height: 1.6; 
                resize: none; 
                box-sizing: border-box;
                display: block;
              }
              
              .deck0-shadow-controls { position: absolute; bottom: 13px; left: 18px; display: flex; align-items: center; gap: 4px; z-index: 10; }
              .deck0-shadow-badge-wrapper { position: relative; }
              
              .deck0-shadow-badge {
                font-size: 9px;
                font-weight: bold;
                padding: 2px 6px;
                border-radius: 4px;
                cursor: pointer;
                border: 1px solid rgba(255,255,255,0.1);
                background: rgba(255,255,255,0.05);
                color: #ccc;
                user-select: none;
                transition: background 0.2s;
              }
              .deck0-shadow-badge:hover { background: rgba(255,255,255,0.1); }
              
              .deck0-shadow-send-btn {
                position: absolute; 
                bottom: 13px; 
                right: 18px; 
                width: 26px; 
                height: 26px; 
                display: flex; 
                align-items: center; 
                justify-content: center; 
                cursor: pointer; 
                border-radius: 4px; 
                background: rgba(255,255,255,0.05); 
                color: #ccc; 
                transition: all 0.2s;
                z-index: 10;
              }
              .deck0-shadow-send-btn:hover { background: rgba(255,255,255,0.1); }
              
              .deck0-shadow-menu {
                position: absolute;
                bottom: 28px;
                left: 0;
                min-width: 160px;
                background: #252526;
                border: 1px solid #454545;
                border-radius: 4px;
                display: none;
                flex-direction: column;
                box-shadow: 0 4px 12px rgba(0,0,0,0.5);
                z-index: 100;
                padding: 1px 0;
              }
              .deck0-shadow-menu.visible { display: flex; }
              
              .deck0-shadow-menu-item {
                padding: 6px 12px;
                font-size: 11px;
                color: #ccc;
                cursor: pointer;
                border-left: 3px solid transparent;
                display: flex;
                align-items: center;
                gap: 8px;
              }
              .deck0-shadow-menu-item:hover { background: rgba(255,255,255,0.05); color: #fff; }
              .deck0-shadow-menu-dot { width: 8px; height: 8px; border-radius: 50%; }

              /* Task List Progress & Rows */
              .deck0-tasks-progress { margin: 1px 8px; border: 1px solid rgba(255,255,255,0.06); background: rgba(42,45,46,0.7); border-radius: 4px; overflow: hidden; margin-bottom: 8px; }
              .deck0-tasks-progress-row { display: flex; align-items: center; justify-content: space-between; gap: 10px; padding: 1px 10px; min-height: 22px; }
              .deck0-tasks-progress-left { display: flex; align-items: center; gap: 8px; flex: 1; min-width: 0; }
              .deck0-tasks-progress-badge { font-size: 11px; font-weight: 700; padding: 1px 4px; border-radius: 6px; background: rgba(255,255,255,0.1); color: #c5c5c5; flex-shrink: 0; }
              .deck0-tasks-progress-text { font-size: 12px; font-weight: 600; color: #c5c5c5; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
              .deck0-tasks-progress-right { display: flex; align-items: center; gap: 8px; flex-shrink: 0; color: #858585; }
              .deck0-tasks-progress-count { font-size: 10px; font-family: monospace; }
              .deck0-spinner { width: 12px; height: 12px; border-radius: 50%; border: 2px solid rgba(255,255,255,0.18); border-top-color: #0a84ff; animation: deck0-spin 0.9s linear infinite; }
              .deck0-tasks-progress-check { font-size: 12px; color: #30d158; font-weight: 900; }
              .deck0-tasks-progress-underline { height: 3px; width: 0%; background: #30d158; transition: width 0.25s ease; opacity: 0.9; }
              @keyframes deck0-spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
              
              .deck0-task-row { display: flex; align-items: center; padding: 4px 10px; border-bottom: 1px solid rgba(255,255,255,0.03); cursor: pointer; transition: background 0.1s; position: relative; font-size: 11px; height: 28px; }
              .deck0-task-row:hover { background: rgba(255,255,255,0.05); }
              .deck0-task-status-dot { width: 6px; height: 6px; border-radius: 50%; margin-right: 8px; background: #444; flex-shrink: 0; transition: all 0.3s; }
              .deck0-task-status-dot.pending { background: #555; }
              .deck0-task-status-dot.running { background: #ff9f0a; box-shadow: 0 0 5px #ff9f0a; animation: pulse 1s infinite; }
              .deck0-task-status-dot.done { background: #30d158; }
              .deck0-task-id { font-family: monospace; color: #666; margin-right: 8px; font-size: 9px; min-width: 45px; }
              .deck0-task-title { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; color: #ccc; }
              .deck0-task-actions { display: flex; gap: 4px; opacity: 0; transition: opacity 0.2s; margin-left: 8px; }
              .deck0-task-row:hover .deck0-task-actions { opacity: 1; }
              .deck0-plan-marker { position: absolute; bottom: 6px; right: 6px; width: 16px; height: 16px; z-index: 5; pointer-events: none; opacity: 0; transform: scale(0.5); transition: all 0.3s; }
              .deck0-plan-marker.visible { opacity: 1; transform: scale(1); }
              .deck0-tag { font-size: 9px; padding: 2px 5px; border-radius: 4px; font-weight: 700; margin-right: 6px; letter-spacing: 0.5px; display: inline-block; min-width: 36px; text-align: center; flex-shrink: 0; }
            

              .deck0-accordion-title { display: flex; align-items: center; gap: 6px; }
              .deck0-accordion-arrow { font-size: 8px; transform: rotate(-90deg); transition: transform 0.2s; }
              .deck0-accordion.open .deck0-accordion-arrow { transform: rotate(0deg); }
              .deck0-accordion-actions { display: flex; gap: 4px; }
              .deck0-empty-message { padding: 10px; text-align: center; color: #666; font-size: 10px; }
              
              /* (Redundant Input Components CSS Removed) */
              
              /* TaskList Utilities */
              .deck0-badge-done { background: rgba(48, 209, 88, 0.25) !important; color: #30d158 !important; }
              .deck0-progress-visible { opacity: 0.9; }
              .deck0-progress-hidden { opacity: 0; }
              
              /* Tag Colors */
              .deck0-tag-infra { color: #98989d; }
              .deck0-tag-core { color: #64d2ff; }
              .deck0-tag-api { color: #da8fff; }
              .deck0-tag-ui { color: #ff6482; }
              .deck0-tag-test { color: #ffb340; }
              .deck0-tag-default { color: #888; }
              
              /* Type Colors */
              .deck0-type-feat { color: #30d158; font-weight: bold; margin-right: 4px; }
              .deck0-type-fix { color: #ff453a; font-weight: bold; margin-right: 4px; }
              .deck0-type-refactor { color: #0a84ff; font-weight: bold; margin-right: 4px; }
              .deck0-type-chore { color: #8e8e93; font-weight: bold; margin-right: 4px; }
              .deck0-type-default { color: #ffffff; font-weight: bold; margin-right: 4px; }
            
                        `;
            const styleEl = document.createElement('style');
            styleEl.textContent = shadowCSS;
            shadowRoot.appendChild(styleEl);

            deck0.ui.shadow.root = shadowRoot;
            return shadowRoot;
          };

          // [P6-L1-3] Toggle Functions
          deck0.ui.shadow.enable = () => {
            if (!deck0.ui.shadow.root) deck0.ui.shadow.mount();
            if (deck0.ui.shadow.root) {
              deck0.ui.shadow.root.style.display = 'block';
              deck0.ui.shadow.enabled = true;
              deck0.ui.shadow.render(); // Force render to populate UI
            }
          };

          deck0.ui.shadow.disable = () => {
            if (deck0.ui.shadow.root) {
              deck0.ui.shadow.root.style.display = 'none';
              deck0.ui.shadow.enabled = false;
            }
          };

          deck0.ui.shadow.toggle = () => {
            deck0.ui.shadow.enabled ? deck0.ui.shadow.disable() : deck0.ui.shadow.enable();
            // [P6-SYNC] Sync chat history when enabling
            if (deck0.ui.shadow.enabled && deck0.ui.shadow.adapter) {
              deck0.ui.shadow.adapter.syncHistory();
            }
          };
          deck0.ui.shadow.components.AppShell = () => {
            const container = el('div', {
              cls: 'deck0-shadow-grid deck0-grid',
              style: {
                display: 'grid',
                gridTemplateColumns: '225px 1fr 300px',
                gridTemplateRows: '36px 1fr',
                gridTemplateAreas: `"header header header" "sidebar main right"`,
                height: '100%',
                gap: '0',
                position: 'relative'
              }
            });

            // Left Sidebar Container
            const sidebar = el('div', {
              cls: 'deck0-shadow-sidebar deck0-sidebar',
              style: {
                borderRight: THEME.border,
                background: THEME.panelBg,
                overflow: 'hidden',
                display: 'flex',
                flexDirection: 'column'
              }
            });
            container.appendChild(sidebar);

            // Center Main Container
            const main = el('div', {
              cls: 'deck0-shadow-main deck0-main',
              style: {
                background: THEME.bg,
                overflow: 'hidden',
                display: 'flex',
                flexDirection: 'column'
              }
            });
            container.appendChild(main);

            // Right Panel Container
            const right = el('div', {
              cls: 'deck0-right',
              style: {
                borderLeft: THEME.border,
                background: THEME.panelBg,
                overflow: 'hidden',
                display: 'flex',
                flexDirection: 'column'
              }
            });
            container.appendChild(right);

            // Store refs for child components to mount into
            deck0.ui.shadow.refs.sidebar = sidebar;
            deck0.ui.shadow.refs.main = main;
            deck0.ui.shadow.refs.right = right;

            // Flyout Container (Overlay)
            const flyout = el('div', { id: 'deck0-shadow-flyout' });
            container.appendChild(flyout);
            deck0.ui.shadow.refs.flyout = flyout;
            return container;
          };

          // Shadow render helper (mounts AppShell + all children)
          deck0.ui.shadow.render = () => {
            const root = deck0.ui.shadow.root;
            if (!root) {
              console.warn('Shadow root not mounted');
              return;
            }

            // Clear and mount AppShell first (preserve <style> tag)
            const existingStyle = root.querySelector('style');
            root.replaceChildren();
            if (existingStyle) root.appendChild(existingStyle);
            const shell = deck0.ui.shadow.components.AppShell();
            root.appendChild(shell);

            // Mount logic: TopBar goes INTO shell, others go into refs
            if (typeof deck0.ui.shadow.components.TopBar === 'function') {
              // TopBar is part of the grid now, append directly to shell container
              shell.appendChild(deck0.ui.shadow.components.TopBar());
            }

            // Mount child components into their containers
            const refs = deck0.ui.shadow.refs;

            // Left sidebar: FileTree
            if (refs.sidebar && typeof deck0.ui.shadow.components.FileTree === 'function') {
              refs.sidebar.appendChild(deck0.ui.shadow.components.FileTree());
            }

            // Main: Editor
            if (refs.main && typeof deck0.ui.shadow.components.Editor === 'function') {
              refs.main.appendChild(deck0.ui.shadow.components.Editor());
            }

            // Right panel: Tasks, Files, StreamView
            if (refs.right) {
              if (typeof deck0.ui.shadow.components.RightHeader === 'function') {
                refs.right.appendChild(deck0.ui.shadow.components.RightHeader());
              }
              if (typeof deck0.ui.shadow.components.StreamView === 'function') {
                refs.right.appendChild(deck0.ui.shadow.components.StreamView());
              }
              if (typeof deck0.ui.shadow.components.TaskList === 'function') {
                refs.right.appendChild(deck0.ui.shadow.components.TaskList());
              }
              if (typeof deck0.ui.shadow.components.ChangedFiles === 'function') {
                refs.right.appendChild(deck0.ui.shadow.components.ChangedFiles());
              }
              // [P6-BEHAVIOR] Use renderInputBox orchestration
              if (deck0.ui.shadow.renderInputBox) {
                deck0.ui.shadow.renderInputBox(refs.right);
              }
            }
          };

          // [P6-L3] Right Header Shadow Component
          deck0.ui.shadow.components.RightHeader = () => {
            return el('div', {
              cls: 'deck0-tree-item deck0-panel-header',
              children: [
                el('span', { text: 'CODE ASSISTANT' })
              ]
            });
          };

          // [P6-L3] TopBar Shadow Component
          deck0.ui.shadow.components.TopBar = () => {
            const state = store.getState();
            const storageMode = state.project?.storageMode || 'LOCAL';

            const bar = el('div', {
              cls: 'deck0-shadow-topbar deck0-header',
              style: {
                gridArea: 'header',
                borderBottom: THEME.border,
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'space-between',
                padding: '0 16px',
                fontSize: '12px',
                background: 'rgba(0,0,0,0.2)',
                position: 'relative',
                width: '100%',
                height: '100%',
                boxSizing: 'border-box'
              }
            });

            // Left: Title
            const title = el('div', {
              text: 'Deck 0 mini (Shadow UI)',
              style: {
                fontWeight: '700',
                letterSpacing: '0.5px',
                position: 'absolute',
                left: '50%',
                transform: 'translateX(-50%)',
                pointerEvents: 'none',
                userSelect: 'none'
              }
            });
            bar.appendChild(title);

            // Right: Controls
            const controls = el('div', {
              cls: 'deck0-header-right',
              style: {
                marginLeft: 'auto',
                zIndex: '10'
              }
            });
            bar.appendChild(controls);

            // App Controls group
            const appCtrl = el('div', {
              cls: 'deck0-win-ctrl'
            });
            controls.appendChild(appCtrl);

            // Storage Indicator
            const storageIndicator = el('div', {
              id: 'deck0-shadow-storage-ind',
              text: storageMode,
              style: {
                fontSize: '9px',
                fontWeight: 'bold',
                color: '#777',
                paddingRight: '10px',
                cursor: 'pointer'
              },
              click: () => {
                if (typeof deck0.ui.shadow.components.Settings === 'function') {
                  deck0.ui.shadow.components.Settings();
                }
              }
            });
            appCtrl.appendChild(storageIndicator);

            // Settings Button (SVG gear icon)
            const settingsBtn = el('div', {
              cls: 'deck0-settings-btn',
              attrs: { title: 'Settings' },
              click: () => {
                if (typeof deck0.ui.shadow.components.Settings === 'function') {
                  deck0.ui.shadow.components.Settings();
                }
              }
            });
            const gearSvg = el('svg', {
              attrs: {
                viewBox: '0 0 16 16',
                width: '14',
                height: '14',
                fill: 'currentColor'
              }
            });
            const gearPath = el('path', {
              attrs: {
                d: 'M9.405 1.05c-.413-1.4-2.397-1.4-2.81 0l-.1.34a1.464 1.464 0 0 1-2.105.872l-.31-.17c-1.283-.698-2.686.705-1.987 1.987l.169.311c.446.82.023 1.841-.872 2.105l-.34.1c-1.4.413-1.4 2.397 0 2.81l.34.1a1.464 1.464 0 0 1 .872 2.105l-.17.31c-.698 1.283.705 2.686 1.987 1.987l.311-.169a1.464 1.464 0 0 1 2.105.872l.1.34c.413 1.4 2.397 1.4 2.81 0l.1-.34a1.464 1.464 0 0 1 2.105-.872l.31.17c1.283.698 2.686-.705 1.987-1.987l-.169-.311a1.464 1.464 0 0 1 .872-2.105l.34-.1c1.4-.413 1.4-2.397 0-2.81l-.34-.1a1.464 1.464 0 0 1-.872-2.105l.17-.31c-.698-1.283-.705-2.686-1.987-1.987l-.311.169a1.464 1.464 0 0 1-2.105-.872l-.1-.34zM8 10.93a2.929 2.929 0 1 1 0-5.86 2.929 2.929 0 0 1 0 5.858z',
                'fill-rule': 'evenodd'
              }
            });
            gearSvg.appendChild(gearPath);
            settingsBtn.appendChild(gearSvg);
            appCtrl.appendChild(settingsBtn);

            // Window Controls (Mac-style dots)
            const winCtrl = el('div', {
              cls: 'deck0-win-ctrl',
              style: {
                paddingLeft: '8px',
                borderLeft: '1px solid rgba(255,255,255,0.1)'
              }
            });
            controls.appendChild(winCtrl);

            // Minimize (yellow dot)
            el('div', {
              cls: 'deck0-win-btn',
              style: { background: '#febc2e' },
              click: () => deck0.ui.shadow.minimize(),
              parent: winCtrl
            });

            // Close (red dot)
            el('div', {
              cls: 'deck0-win-btn',
              style: { background: '#ff5f57' },
              click: () => {
                deck0.ui.shadow.root?.remove();
                deck0.ui.shadow.enabled = false;
              },
              parent: winCtrl
            });

            // Store refs
            deck0.ui.shadow.refs.storageIndicator = storageIndicator;

            return bar;
          };

          // Shadow helpers
          deck0.ui.shadow.minimize = () => {
            const root = deck0.ui.shadow.root;
            if (root) {
              root.style.display = 'none';
              deck0.ui.shadow.enabled = false;
              log('Shadow UI minimized', 'info');
            }
          };

          deck0.ui.shadow.updateStorageUI = (mode) => {
            const indicator = deck0.ui.shadow.refs.storageIndicator;
            if (!indicator) return;
            indicator.textContent = mode || 'LOCAL';
          };

          // [P6-A1] ChangedFiles Shadow Component (Accordion)

          // [P6-COMPONENT] Pure Components for ChangedFiles (S-Tier Refactor)
          // Concept: Structure = UI. Declarative composition via el().

          deck0.ui.shadow.components.FileRow = (file, actions) => {
            const { path, op, fileName, dirName, linesAdded, linesRemoved } = file;
            const opIcon = (op === 'delete' ? '🗑️' : (op === 'write' ? '✨' : '📝'));

            // 1. Structure Definition
            return el('div', {
              cls: `deck0-changed-file-row ${op === 'delete' ? 'deleted' : ''}`,
              click: (e) => { e.preventDefault(); e.stopPropagation(); actions.onClick(path); },
              children: [
                // Left: Icon + Name + Dir
                el('div', {
                  cls: 'deck0-changed-file-main',
                  children: [
                    el('div', { cls: 'deck0-changed-file-icon', text: opIcon }),
                    el('div', { cls: 'deck0-changed-file-name', text: fileName }),
                    dirName ? el('div', { cls: 'deck0-changed-file-dir', text: dirName }) : null
                  ]
                }),
                // Right: Stats + Actions
                el('div', {
                  cls: 'deck0-changed-file-right',
                  children: [
                    el('div', {
                      cls: 'deck0-changed-file-stats',
                      children: [
                        el('span', { cls: 'deck0-files-stat-add', text: `+${linesAdded}` }),
                        el('span', { cls: 'deck0-files-stat-del', text: `-${linesRemoved}` })
                      ]
                    }),
                    el('div', {
                      cls: 'deck0-changed-file-actions',
                      children: [
                        el('div', {
                          cls: 'deck0-btn-mini keep', text: '✓', attrs: { title: 'Keep' },
                          click: (e) => { e.stopPropagation(); actions.onKeep(path); }
                        }),
                        el('div', {
                          cls: 'deck0-btn-mini undo', text: '↺', attrs: { title: 'Undo' },
                          click: (e) => { e.stopPropagation(); actions.onUndo(path); }
                        })
                      ]
                    })
                  ]
                })
              ]
            });
          };

          deck0.ui.shadow.components.FilesCard = (data, actions) => {
            const { totalAdded, totalRemoved, title, files } = data;

            // 1. Header Component
            const Header = el('div', {
              cls: 'deck0-files-card-row',
              children: [
                el('div', { cls: 'deck0-files-card-title', text: title }),
                el('div', {
                  cls: 'deck0-files-card-meta',
                  children: [
                    // Stats
                    el('div', {
                      cls: 'deck0-files-card-stats',
                      children: [
                        el('span', { cls: 'deck0-files-stat-add', text: `+${totalAdded}` }),
                        el('span', { cls: 'deck0-files-stat-del', text: `-${totalRemoved}` })
                      ]
                    }),
                    // Actions
                    el('div', {
                      cls: 'deck0-files-card-actions',
                      children: [
                        el('div', {
                          cls: 'deck0-files-action-btn keep', text: 'KEEP',
                          click: (e) => { e.stopPropagation(); actions.onKeepAll(); }
                        }),
                        el('div', {
                          cls: 'deck0-files-action-btn', text: 'UNDO',
                          click: (e) => { e.stopPropagation(); actions.onUndoAll(); }
                        })
                      ]
                    })
                  ]
                })
              ]
            });

            // 2. List Component
            // Map pure data to pure components
            const ListItems = files.map(file =>
              deck0.ui.shadow.components.FileRow(file, {
                onClick: actions.onClickFile,
                onKeep: actions.onKeepFile,
                onUndo: actions.onUndoFile
              })
            );

            // 3. Card Assembly
            return el('div', {
              cls: 'deck0-files-card',
              children: [Header, ...ListItems]
            });
          };

          // [P6-LOGIC] Business Logic Layer (Data Preparation)
          deck0.ui.shadow.logic.prepareChangedFilesData = async () => {
            try {
              // Robust State Access
              const state = (typeof Deck0 !== 'undefined' && Deck0.store) ? Deck0.store.getState() : (window.Deck0?.store?.getState());
              if (!state) return null;

              const paths = [...new Set(state.session?.syncList || [])].filter(p => p && String(p).trim());
              if (paths.length === 0) return null;

              // Helper: Compute Diff Stats (Use local DiffEngine to avoid scope issues)
              const computeLineStats = (oldText, newText) => {
                const engine = (typeof DiffEngine !== 'undefined') ? DiffEngine : (window.Deck0?.core?.diff);
                if (!engine) return { added: 0, removed: 0 };
                const diffs = engine.compute(oldText || '', newText || '');
                let added = 0, removed = 0;
                for (const d of diffs) {
                  if (d.type === 'add') added++;
                  else if (d.type === 'del') removed++;
                }
                return { added, removed };
              };

              // Parallel Data Fetch
              const files = await Promise.all(paths.map(async (p) => {
                // Ensure methods exist
                const getBaseline = (typeof Deck0 !== 'undefined' && Deck0.getBaselineForPath) ? Deck0.getBaselineForPath : window.Deck0?.getBaselineForPath;
                const readFile = (typeof Deck0 !== 'undefined' && Deck0.readFileContent) ? Deck0.readFileContent : window.Deck0?.readFileContent;

                const baseline = getBaseline ? await getBaseline(p) : { existed: false, content: '' };
                let exists = true;
                let content = '';
                try {
                  if (readFile) content = await readFile(p);
                  else throw new Error('Reader missing');
                } catch (e) { exists = false; }

                const op = !exists ? 'delete' : (baseline.existed ? 'patch' : 'write');
                const stats = computeLineStats(baseline.content, content);

                const cleanPath = String(p || '').replace(/\\/g, '/');
                const parts = cleanPath.split('/').filter(Boolean);
                const fileName = parts.length ? parts[parts.length - 1] : p;
                const dirName = parts.length > 1 ? parts.slice(0, -1).join('/') : '';

                return {
                  path: p, op, exists, fileName, dirName,
                  linesAdded: stats.added, linesRemoved: stats.removed
                };
              }));

              const totalAdded = files.reduce((sum, f) => sum + (Number(f.linesAdded) || 0), 0);
              const totalRemoved = files.reduce((sum, f) => sum + (Number(f.linesRemoved) || 0), 0);
              const title = files.length === 1 ? '1 file changed' : `${files.length} files changed`;

              return {
                files,
                summary: { totalAdded, totalRemoved, title, count: files.length }
              };
            } catch (e) {
              console.error('[Shadow Logic] Data prep failed:', e);
              return null;
            }
          };

          // [P6-BEHAVIOR] Render Entry Point (The Orchestrator)
          deck0.ui.shadow.renderChangedFiles = async () => {
            const { refs, logic, components } = deck0.ui.shadow;
            if (!refs.filesBody) return;

            // 1. Data Phase
            const data = await logic.prepareChangedFilesData();

            // 2. Header Sync
            const count = data ? data.summary.count : 0;
            if (refs.filesHeaderCount) refs.filesHeaderCount.textContent = `CHANGED FILES (${count})`;

            refs.filesBody.replaceChildren();

            // 3. Render Phase (Declarative)
            if (!data) {
              // Empty State
              refs.filesBody.classList.remove('open');
              if (refs.filesBody.parentElement) refs.filesBody.parentElement.classList.remove('open');
              refs.filesBody.appendChild(el('div', { text: 'No changed files', cls: 'deck0-empty-message' }));
              return;
            }

            // Auto-Expand
            if (!refs.filesBody.classList.contains('open')) {
              refs.filesBody.classList.add('open');
              if (refs.filesBody.parentElement) refs.filesBody.parentElement.classList.add('open');
            }

            // Prepare Component Props & Actions
            const props = {
              title: data.summary.title,
              totalAdded: data.summary.totalAdded,
              totalRemoved: data.summary.totalRemoved,
              files: data.files
            };

            const actions = {
              onKeepAll: () => Deck0.keepAllChanges(),
              onUndoAll: () => Deck0.undoChanges(),
              onKeepFile: (p) => Deck0.keepFileChange(p),
              onUndoFile: (p) => Deck0.undoFileChange(p),
              onClickFile: async (p) => { try { await Deck0.enterDiffReviewMode(p); } catch (e) { } }
            };

            // 4. Mount
            const card = components.FilesCard(props, actions);
            refs.filesBody.appendChild(card);
          };

          // [P6-A1] ChangedFiles Shadow Component (Accordion) - Declarative
          deck0.ui.shadow.components.ChangedFiles = () => {
            // Refs to be bound
            let filesHeaderCount = null;
            let filesBody = null;

            const Header = el('div', {
              cls: 'deck0-accordion-header',
              click: (e) => {
                const acc = e.currentTarget.parentElement;
                const body = acc.querySelector('.deck0-accordion-body');
                body.classList.toggle('open');
                acc.classList.toggle('open');
              },
              children: [
                el('div', {
                  cls: 'deck0-accordion-title',
                  children: [
                    el('span', { cls: 'deck0-acc-arrow deck0-accordion-arrow', text: '▼' }),
                    (filesHeaderCount = el('span', { text: 'CHANGED FILES (0)' }))
                  ]
                })
              ]
            });

            const Body = (filesBody = el('div', { cls: 'deck0-accordion-body' }));

            // Bind Refs
            deck0.ui.shadow.refs.filesHeaderCount = filesHeaderCount;
            deck0.ui.shadow.refs.filesBody = filesBody;

            // Hydrate Logic
            if (deck0.ui.shadow.renderChangedFiles) {
              setTimeout(deck0.ui.shadow.renderChangedFiles, 0);
            }

            // Return Structure
            return el('div', {
              cls: 'deck0-accordion',
              children: [Header, Body]
            });
          };

          // [P6-A2] TaskList Shadow Component (Shell)
          deck0.ui.shadow.components.TaskList = () => {
            let tasksHeaderCount, taskListBody;

            // Global Actions (Batch)
            const batchActions = {
              onRunAll: () => Deck0.executeChanges(),
              onKeepAll: () => Deck0.keepAllChanges(),
              onUndoAll: () => Deck0.undoChanges()
            };

            const Header = el('div', {
              cls: 'deck0-accordion-header',
              click: (e) => {
                if (e.target.closest('.deck0-btn-mini')) return;
                const acc = e.currentTarget.parentElement;
                acc.classList.toggle('open');
                const body = acc.querySelector('.deck0-accordion-body');
                body.classList.toggle('open');
              },
              children: [
                el('div', {
                  cls: 'deck0-accordion-title',
                  children: [
                    el('span', { text: '▼', cls: 'deck0-acc-arrow deck0-accordion-arrow' }),
                    (tasksHeaderCount = el('span', { text: 'TASKS (0)' }))
                  ]
                }),
                el('div', {
                  cls: 'deck0-accordion-actions',
                  children: [
                    el('div', {
                      cls: 'deck0-btn-mini run', text: '▶', attrs: { title: 'Execute All' },
                      click: (e) => { e.stopPropagation(); batchActions.onRunAll(); }
                    }),
                    el('div', {
                      cls: 'deck0-btn-mini keep', text: '✓', attrs: { title: 'KEEP ALL' },
                      click: (e) => { e.stopPropagation(); batchActions.onKeepAll(); }
                    }),
                    el('div', {
                      cls: 'deck0-btn-mini undo', text: '↺', attrs: { title: 'Undo All' },
                      click: (e) => { e.stopPropagation(); batchActions.onUndoAll(); }
                    })
                  ]
                })
              ]
            });

            const Body = (taskListBody = el('div', { cls: 'deck0-accordion-body' }));

            // Bind Refs
            deck0.ui.shadow.refs.tasksHeaderCount = tasksHeaderCount;
            deck0.ui.shadow.refs.taskList = taskListBody;

            // Hydrate
            if (deck0.ui.shadow.renderTaskList) {
              setTimeout(deck0.ui.shadow.renderTaskList, 0);
            }

            return el('div', {
              cls: 'deck0-accordion',
              children: [Header, Body]
            });
          };

          // [P6-BEHAVIOR] InputBox Renderer (Orchestrator)
          deck0.ui.shadow.renderInputBox = (container) => {
            const { refs, logic, components } = deck0.ui.shadow;

            // 1. Clean up existing (avoid duplication)
            const existing = container.querySelector('.deck0-shadow-input-container');
            if (existing) existing.remove();

            // 2. Data Phase
            const data = logic.prepareInputData();
            if (!data) return; // Store not ready

            // 3. Actions Phase
            const actions = {
              onSendClick: () => Deck0.orchestrator.run(),
              onKeydown: (e) => {
                if (e.key === 'Enter' && !e.shiftKey && !e.ctrlKey && !e.metaKey) {
                  e.preventDefault();
                  Deck0.orchestrator.run();
                }
              },
              onInput: (e) => {
                const el = e.target;
                // Auto-Resize
                el.style.height = 'auto';
                el.style.height = Math.min(el.scrollHeight, 200) + 'px';

                // Sync Forward (Shadow -> Legacy)
                const legacyInput = document.querySelector('.deck0-input-area');
                if (legacyInput && legacyInput.value !== el.value) {
                  legacyInput.value = el.value;
                  // Don't dispatch 'input' here to avoid loop if legacy listener is active, 
                  // or dispatch if needed for legacy app logic. 
                  // Safe bet: Programmatic set is enough for runPipeline extraction.
                }
              },
              toggleExpertMenu: () => {
                const menu = renderResult.refs.expertMenu;
                if (menu) menu.classList.toggle('visible');
                if (renderResult.refs.modelMenu) renderResult.refs.modelMenu.classList.remove('visible');
              },
              toggleModelMenu: () => {
                const menu = renderResult.refs.modelMenu;
                if (menu) menu.classList.toggle('visible');
                if (renderResult.refs.expertMenu) renderResult.refs.expertMenu.classList.remove('visible');
              },
              setExpert: (id) => {
                Deck0.store.mutate('settings', s => { s.expertId = id; });
                deck0.ui.shadow.renderInputBox(container); // Re-render for theme update
              },
              setModel: (id) => {
                Deck0.store.mutate('settings', s => { s.model = id; });
                deck0.ui.shadow.renderInputBox(container); // Re-render for label update
              }
            };

            // 4. Render Phase
            const renderResult = components.InputBox(data, actions);

            // 5. Sync Phase (Bidirectional)
            // Critical for runPipeline compatibility until legacy UI is removed
            const inputEl = renderResult.refs.input;
            if (inputEl) {
              // Initial value sync
              const legacyInput = document.querySelector('.deck0-input-area');
              if (legacyInput) {
                inputEl.value = legacyInput.value;
                // Initial Resize
                setTimeout(() => {
                  inputEl.style.height = 'auto';
                  inputEl.style.height = Math.min(inputEl.scrollHeight, 200) + 'px';
                }, 0);
              }

              // Backward Sync (Legacy -> Shadow)
              if (refs.legacySyncHandler && refs.legacyInputRef) {
                refs.legacyInputRef.removeEventListener('input', refs.legacySyncHandler);
              }

              if (legacyInput) {
                const backwardHandler = () => {
                  if (inputEl.value !== legacyInput.value) {
                    inputEl.value = legacyInput.value;
                    // Auto-Resize on sync
                    inputEl.style.height = 'auto';
                    inputEl.style.height = Math.min(inputEl.scrollHeight, 200) + 'px';
                  }
                };
                legacyInput.addEventListener('input', backwardHandler);
                // Store for cleanup
                refs.legacySyncHandler = backwardHandler;
                refs.legacyInputRef = legacyInput;
              }
            }

            // 6. Mount Phase
            container.appendChild(renderResult.element);

            // 7. Register Refs
            refs.input = renderResult.refs.input;
          };

          // [P6-COMPONENT] Input Micro-Components (Strict Class-Based)
          deck0.ui.shadow.components.Menu = (items, activeValue, onSelect, type = 'text') => {
            return el('div', {
              cls: 'deck0-shadow-menu',
              children: items.map(item => {
                const val = typeof item === 'string' ? item : item.id;
                const label = typeof item === 'string' ? item : item.name;
                const isActive = val === activeValue;

                const children = [];
                if (type === 'color' && item.color) {
                  // Use generated class for color
                  children.push(el('div', { cls: `deck0-shadow-menu-dot deck0-dot-${item.id}` }));
                }
                children.push(el('span', { text: label }));

                return el('div', {
                  cls: `deck0-shadow-menu-item ${isActive ? 'active' : ''}`,
                  children: children,
                  click: (e) => { e.stopPropagation(); onSelect(val); }
                });
              })
            });
          };

          deck0.ui.shadow.components.Badge = (text, onClick) => {
            return el('div', {
              cls: 'deck0-shadow-badge',
              text: text,
              click: (e) => { e.stopPropagation(); onClick(e); }
            });
          };

          deck0.ui.shadow.components.SendButton = (isRunning, onClick) => {
            let icon;
            if (isRunning) {
              // Stop Icon (Square) - SVG
              icon = el('svg', { attrs: { viewBox: '0 0 24 24', width: '12', height: '12', fill: 'currentColor' } });
              icon.appendChild(el('rect', { attrs: { x: '6', y: '6', width: '12', height: '12' } }));
            } else {
              // Send Icon (Plane) - SVG
              icon = el('svg', { attrs: { viewBox: '0 0 24 24', width: '16', height: '16', fill: 'currentColor' } });
              icon.appendChild(el('path', { attrs: { d: 'M2.01 21L23 12 2.01 3 2 10l15 2-15 2z' } }));
            }

            return el('div', {
              cls: 'deck0-shadow-send-btn',
              click: onClick,
              children: [icon]
            });
          };

          deck0.ui.shadow.components.InputBox = (props, actions) => {
            const refs = {};

            // 1. Textarea
            const textarea = el('textarea', {
              cls: 'deck0-shadow-textarea',
              attrs: { placeholder: props.expertDesc },
              keydown: (e) => actions.onKeydown(e),
              // [V19.1] Expose Input Event for Auto-Resize & Sync
              children: [], // No children for textarea
            });
            textarea.addEventListener('input', (e) => actions.onInput(e));
            refs.input = textarea;

            // 2. Controls Row
            const expertBadge = deck0.ui.shadow.components.Badge(props.expertName, actions.toggleExpertMenu);
            const expertMenu = deck0.ui.shadow.components.Menu(props.experts, props.expertId, actions.setExpert, 'color');
            refs.expertMenu = expertMenu;

            const modelBadge = deck0.ui.shadow.components.Badge('Model', actions.toggleModelMenu);
            const modelMenu = deck0.ui.shadow.components.Menu(props.models, props.model, actions.setModel, 'text');
            refs.modelMenu = modelMenu;

            const controls = el('div', {
              cls: 'deck0-shadow-controls',
              children: [
                el('div', { cls: 'deck0-shadow-badge-wrapper', children: [expertBadge, expertMenu] }),
                el('div', { cls: 'deck0-shadow-badge-wrapper', children: [modelBadge, modelMenu] })
              ]
            });

            // 3. Send Button
            const sendBtn = deck0.ui.shadow.components.SendButton(props.isRunning, actions.onSendClick);

            // Assembly (Theme class applied here)
            const wrapper = el('div', {
              cls: `deck0-shadow-input-container deck0-theme-${props.expertId}`,
              children: [textarea, controls, sendBtn]
            });

            return { element: wrapper, refs };
          };

          // [P6-A3] StreamView Shadow Component
          deck0.ui.shadow.components.StreamView = () => {
            const container = el('div', {
              cls: 'deck0-shadow-stream-view deck0-chat-history'
              // Styles defined in Shadow CSS injection
            });

            // Store ref for message APIs
            deck0.ui.shadow.refs.chatHistory = container;

            return container;
          };

          // Shadow addChatMessage helper (uses cls, no inline styles)
          deck0.ui.shadow.addChatMessage = (role, content) => {
            const chatHistory = deck0.ui.shadow.refs.chatHistory;
            if (!chatHistory) return null;

            const isUser = role === 'user';
            const bubble = el('div', {
              cls: `deck0-bubble deck0-bubble-${isUser ? 'user' : 'assistant'}`
            });

            // Content container
            const contentEl = el('div', { cls: 'deck0-msg-content' });
            contentEl.textContent = content;
            bubble.appendChild(contentEl);

            // Action buttons for assistant bubbles
            if (!isUser) {
              const actions = el('div', { cls: 'deck0-chat-actions' });
              bubble.appendChild(actions);

              // Retry Button
              const retryBtn = el('div', { cls: 'deck0-chat-action-btn', attrs: { title: 'Retry' } });
              retryBtn.appendChild(Icon('retry'));
              retryBtn.onclick = () => deck0.ui.shadow.handleRetry && deck0.ui.shadow.handleRetry(); // Connect Action
              actions.appendChild(retryBtn);

              // Thumbs Up Button
              const thumbsUpBtn = el('div', { cls: 'deck0-chat-action-btn' });
              thumbsUpBtn.appendChild(Icon('thumbsUp'));
              actions.appendChild(thumbsUpBtn);

              // Thumbs Down Button
              const thumbsDownBtn = el('div', { cls: 'deck0-chat-action-btn' });
              thumbsDownBtn.appendChild(Icon('thumbsDown'));
              actions.appendChild(thumbsDownBtn);
            }

            chatHistory.appendChild(bubble);
            chatHistory.scrollTop = chatHistory.scrollHeight;

            return bubble;
          };

          // Shadow addThinkingBubble helper (collapsible thinking container)
          deck0.ui.shadow.addThinkingBubble = () => {
            const chatHistory = deck0.ui.shadow.refs.chatHistory;
            if (!chatHistory) return null;

            const container = el('div', {
              cls: 'deck0-thinking-container'
              // Styles from original CSS (line 1624)
            });

            const header = el('div', {
              cls: 'deck0-thinking-header'
              // Styles from original CSS (line 1625)
            });

            const arrow = el('span', {
              cls: 'deck0-thinking-arrow',
              text: '▼'
              // Styles from original CSS (line 1629)
            });
            header.appendChild(arrow);

            el('span', { text: 'Thinking Process', parent: header });
            container.appendChild(header);

            const content = el('div', {
              cls: 'deck0-thinking-content open'
              // Styles from original CSS (line 1627-1628)
            });
            container.appendChild(content);

            // Toggle functionality
            header.onclick = () => {
              const isOpen = content.classList.contains('open');
              content.classList.toggle('open');
              header.classList.toggle('active');
            };

            chatHistory.appendChild(container);

            // Store ref for appending content
            deck0.ui.shadow.refs.thinkingContent = content;

            return container;
          };

          // Shadow appendThinkingContent helper
          deck0.ui.shadow.appendThinkingContent = (text) => {
            const content = deck0.ui.shadow.refs.thinkingContent;
            if (!content) return;
            content.textContent += text;
            content.scrollTop = content.scrollHeight;
          };

          // Shadow collapseThinking helper
          deck0.ui.shadow.collapseThinking = () => {
            const content = deck0.ui.shadow.refs.thinkingContent;
            if (!content) return;
            content.classList.remove('open');
            const header = content.parentElement?.querySelector('.deck0-thinking-header');
            if (header) header.classList.remove('active');
          };

          // [P6-HELPER] Auto-scroll helper (Component layer)
          deck0.ui.shadow.scrollToBottom = () => {
            const chatHistory = deck0.ui.shadow.refs.chatHistory;
            if (chatHistory) {
              chatHistory.scrollTop = chatHistory.scrollHeight;
            }
          };

          // [P6-ACTION] Retry Handler (Behavior Layer)
          deck0.ui.shadow.handleRetry = async () => {
            if (!confirm('Retry? This will undo changes and let you edit the prompt.')) return;

            // 0. Capture last user message
            let textToRestore = Deck0.store.getState().session.lastUserInstruction;
            try {
              // Try to get from DOM (most recent user bubble)
              const chatHistory = document.querySelector('.deck0-chat-history:not(.deck0-shadow-stream-view)');
              if (chatHistory && chatHistory.children.length > 0) {
                const lastUserBubble = Array.from(chatHistory.children).reverse().find(el =>
                  !el.classList.contains('deck0-assistant-bubble') &&
                  !el.classList.contains('deck0-thinking-container')
                );
                if (lastUserBubble) textToRestore = lastUserBubble.textContent;
              }
            } catch (e) { console.warn('[Shadow UI] Retry capture error', e); }

            // 1. Undo changes
            if (SnapshotManager.currentConversationId) {
              await SnapshotManager.undoConversation();
            }
            Deck0Actions.session.resetPendingChanges();
            renderChangedFiles();

            // 2. Clear chat (both UIs for transition period)
            const origChat = document.querySelector('.deck0-chat-history:not(.deck0-shadow-stream-view)');
            if (origChat) origChat.replaceChildren();

            if (Deck0.ui.shadow.refs?.chatHistory) {
              Deck0.ui.shadow.refs.chatHistory.replaceChildren();
            }

            // 3. Restore input
            const activeInput = document.querySelector(DECK0_SELECTORS.UI_CHAT_INPUT);
            if (activeInput && textToRestore) {
              activeInput.value = textToRestore;
              activeInput.dispatchEvent(new Event('input', { bubbles: true }));
              // activeInput.focus(); // [MOD] Don't steal focus if Shadow UI handled it
              Deck0Actions.session.setLastUserInstruction(textToRestore);
            }

            // [P6-SYNC] Restore to Shadow UI Input
            if (deck0.ui.shadow.refs?.input && textToRestore) {
              deck0.ui.shadow.refs.input.value = textToRestore;
              deck0.ui.shadow.refs.input.focus(); // Prefer Shadow UI focus
            } else if (activeInput) {
              activeInput.focus(); // Fallback focus
            }
          };
        };

        const modules = () => ({
          constants: { Deck0Constants: getConstants(), Deck0Debug: DECK0_DEBUG },
          core: { PromptPack, Deck0Core: getCore() },
          state: { store },
          effects: { Deck0Effects },
          storage: { VfsManager, CommitDriver },
          executor: { Executor, SnapshotManager },
          timeline: { TimelineDB },
          protocol: { Transport, GeminiProvider, DomOnlyProvider, StreamingParser, StreamingPatchParser, ProtocolEngine },
          ui: { BridgeUIAdapter },
          orchestrator: { Orchestrator }
        });

        const boot = () => {
          expose();

          // [TP-P4-1] UI Infrastructure (must run before Editor init)
          Deck0UI.init(); // Inject CSS + create root DOM

          // [TP-P3-2] Module Initialization (Centralized Lifecycle)
          EditorManager.init();
          setExpert('ARCHITECT');

          // Startup Sequence (legacy parity)
          loadTimelineSnapshots();
          updateStorageUI();
          renderExecutionLog();
          setupTimelineHotkeys();

          // [P6-L1-4] Shadow Toggle Hotkey
          document.addEventListener('keydown', (e) => {
            if (e.altKey && e.code === 'KeyH') {
              e.preventDefault();
              Deck0.ui.shadow.toggle();
              log(`Shadow UI ${Deck0.ui.shadow.enabled ? 'Enabled' : 'Disabled'}`, 'info');
            }
          });
          if (Deck0.store.getState().project.storageMode === 'VIRTUAL' || Deck0.store.getState().project.storageMode === 'LINK') Deck0.orchestrator.mount();
          hijackInteractions(); // [V18.0]

          // Init Bridge
          GeminiProvider.init();
          ProtocolEngine.init();
          BridgeUIAdapter.init();
          if (Deck0.ui.shadow?.adapter) Deck0.ui.shadow.adapter.init(); // [P6-BEHAVIOR] Init Shadow UI adapter
        };

        return { boot, modules };
      })();

      Deck0Kernel.boot();

      // [V19.1 DIAGNOSTICS] Unified Health Check (Deck0.diagnose)
      (function initDiagnostics() {
        if (!window.Deck0) return;
        window.Deck0.diagnose = () => {
          const d = window.Deck0;
          console.clear();
          console.group('🏥 Deck0 System Diagnosis');

          // --- SECTION A: PERMANENT ARCHITECTURE (The "Spine") ---
          const core = [
            { layer: 'L4 STATE', check: 'Deck0.store', ok: !!d.state?.store },
            { layer: 'L4 STATE', check: 'Deck0.actions', ok: !!d.actions?.runtime },
            { layer: 'L4 STATE', check: 'Action: clearExecutionLog', ok: typeof d.actions?.session?.clearExecutionLog === 'function' },
            { layer: 'L3 EFFECTS', check: 'Deck0.effects.persist', ok: !!d.effects?.persist },
            { layer: 'L7 PROTOCOL', check: 'Deck0.protocol.engine', ok: !!d.protocol?.engine },
            { layer: 'L6 EXECUTOR', check: 'Deck0.executor', ok: !!d.executor },
            { layer: 'L5 STORAGE', check: 'Deck0.vfs', ok: !!d.vfs },
            { layer: 'L8 UI', check: 'Deck0.ui.refs', ok: !!d.ui?.refs },
            { layer: 'L9 DEBUG', check: 'Deck0.f12', ok: !!d.f12 }
          ];
          console.log('%c1. Core Architecture (Permanent)', 'font-weight:bold; color:#64d2ff');
          console.table(core);

          // --- SECTION B: CRITICAL SAFETY (RL-1) ---
          const safety = [
            { id: 'RL-1', check: 'window.originalXHR', ok: !!window.originalXHR },
            { id: 'RL-1', check: 'window.originalFetch', ok: !!window.originalFetch },
            { id: 'RL-1', check: 'window.deck0KernelActive', ok: !!window.deck0KernelActive },
            { id: 'RL-2', check: 'Stream Parser Active', ok: !!d.protocol?.engine },
            { id: 'RL-7', check: 'GitLink Module', ok: !!d.git }
          ];
          console.log('%c2. Critical Safety (RL-1 Constraints)', 'font-weight:bold; color:#da8fff');
          console.table(safety);

          // --- SECTION C: STATE SNAPSHOT ---
          let stateSummary = 'Error reading state';
          try {
            const s = d.state.store.getState();
            stateSummary = {
              runId: s.runtime.runId,
              mode: s.project.storageMode,
              plans: s.protocol.plans.length,
              syncList: s.session.syncList.length,
              snapshots: s.timeline.snapshots.length,
              view: s.legacy?.DECK0_VIEW?.mode || 'normal'
            };
          } catch (e) { }
          console.log('%c3. State Snapshot', 'font-weight:bold; color:#30d158', stateSummary);

          // --- SECTION D: MIGRATION AUDIT (Temporary / To Delete) ---
          // [!] This section validates the success of P5 Cleanup
          const forbidden = Object.keys(window).filter(k => /^V60_|^DECK0_/.test(k));
          const migration = [
            { task: 'P5-1', check: 'No V60_/DECK0_ globals', ok: forbidden.length === 0, detail: forbidden.slice(0, 3).join(',') },
            { task: 'P5-5', check: 'window.writeFile removed', ok: typeof window.writeFile === 'undefined' },
            { task: 'P5-5', check: 'window.deleteFile removed', ok: typeof window.deleteFile === 'undefined' },
            { task: 'P5-1', check: 'window.mountProject removed', ok: typeof window.mountProject === 'undefined' },
            { task: 'P5-1', check: 'window.runPipeline removed', ok: typeof window.runPipeline === 'undefined' },
            { task: 'P2', check: 'TimelineDB Homing', ok: !!d.timeline?.db },
            { task: 'P2', check: 'Snapshot Homing', ok: !!d.executor?.snapshot },
            { task: 'P2', check: 'Editor Homing', ok: !!d.ui?.editor }
          ];
          console.log('%c4. Migration Audit (Temporary)', 'font-weight:bold; color:#ffb340');
          console.table(migration);

          // Final Verdict
          const allOk = [...core, ...safety, ...migration].every(i => i.ok);
          if (allOk) console.log('%c✅ SYSTEM HEALTHY (Ready for Release)', 'color:#30d158; font-weight:bold; font-size:16px; padding:8px; border: 2px solid #30d158; border-radius: 4px;');
          else console.log('%c⚠️ ISSUES DETECTED - CHECK TABLES', 'color:#ff453a; font-weight:bold; font-size:16px; padding:8px; border: 2px solid #ff453a; border-radius: 4px;');

          console.groupEnd();
          return 'Diagnosis Complete';
        };
      })();

      // [V19.1 DIAGNOSTICS] Unified Health Check (Deck0.diagnose)
      (function initDiagnostics() {
        if (!window.Deck0) return;
        window.Deck0.diagnose = () => {
          const d = window.Deck0;
          console.clear();
          console.group('🏥 Deck0 System Diagnosis');

          // --- SECTION A: PERMANENT ARCHITECTURE (The "Spine") ---
          const core = [
            { layer: 'L4 STATE', check: 'Deck0.store', ok: !!d.state?.store },
            { layer: 'L4 STATE', check: 'Deck0.actions', ok: !!d.actions?.runtime },
            { layer: 'L4 STATE', check: 'Action: clearExecutionLog', ok: typeof d.actions?.session?.clearExecutionLog === 'function' },
            { layer: 'L3 EFFECTS', check: 'Deck0.effects.persist', ok: !!d.effects?.persist },
            { layer: 'L7 PROTOCOL', check: 'Deck0.protocol.engine', ok: !!d.protocol?.engine },
            { layer: 'L6 EXECUTOR', check: 'Deck0.executor', ok: !!d.executor },
            { layer: 'L5 STORAGE', check: 'Deck0.vfs', ok: !!d.vfs },
            { layer: 'L8 UI', check: 'Deck0.ui.refs', ok: !!d.ui?.refs },
            { layer: 'L9 DEBUG', check: 'Deck0.f12', ok: !!d.f12 }
          ];
          console.log('%c1. Core Architecture (Permanent)', 'font-weight:bold; color:#64d2ff');
          console.table(core);

          // --- SECTION B: CRITICAL SAFETY (RL-1) ---
          const safety = [
            { id: 'RL-1', check: 'window.originalXHR', ok: !!window.originalXHR },
            { id: 'RL-1', check: 'window.originalFetch', ok: !!window.originalFetch },
            { id: 'RL-1', check: 'window.deck0KernelActive', ok: !!window.deck0KernelActive },
            { id: 'RL-2', check: 'Stream Parser Active', ok: !!d.protocol?.engine },
            { id: 'RL-7', check: 'GitLink Module', ok: !!d.git }
          ];
          console.log('%c2. Critical Safety (RL-1 Constraints)', 'font-weight:bold; color:#da8fff');
          console.table(safety);

          // --- SECTION C: STATE SNAPSHOT ---
          let stateSummary = 'Error reading state';
          try {
            const s = d.state.store.getState();
            stateSummary = {
              runId: s.runtime.runId,
              mode: s.project.storageMode,
              plans: s.protocol.plans.length,
              syncList: s.session.syncList.length,
              snapshots: s.timeline.snapshots.length,
              view: s.legacy?.DECK0_VIEW?.mode || 'normal'
            };
          } catch (e) { }
          console.log('%c3. State Snapshot', 'font-weight:bold; color:#30d158', stateSummary);

          // --- SECTION D: MIGRATION AUDIT (Temporary / To Delete) ---
          // [!] This section validates the success of P5 Cleanup
          const forbidden = Object.keys(window).filter(k => /^V60_|^DECK0_/.test(k));
          const migration = [
            { task: 'P5-1', check: 'No V60_/DECK0_ globals', ok: forbidden.length === 0, detail: forbidden.slice(0, 3).join(',') },
            { task: 'P5-5', check: 'window.writeFile removed', ok: typeof window.writeFile === 'undefined' },
            { task: 'P5-5', check: 'window.deleteFile removed', ok: typeof window.deleteFile === 'undefined' },
            { task: 'P5-1', check: 'window.mountProject removed', ok: typeof window.mountProject === 'undefined' },
            { task: 'P5-1', check: 'window.runPipeline removed', ok: typeof window.runPipeline === 'undefined' },
            { task: 'P2', check: 'TimelineDB Homing', ok: !!d.timeline?.db },
            { task: 'P2', check: 'Snapshot Homing', ok: !!d.executor?.snapshot },
            { task: 'P2', check: 'Editor Homing', ok: !!d.ui?.editor }
          ];
          console.log('%c4. Migration Audit (Temporary)', 'font-weight:bold; color:#ffb340');
          console.table(migration);

          // Final Verdict
          const allOk = [...core, ...safety, ...migration].every(i => i.ok);
          if (allOk) console.log('%c✅ SYSTEM HEALTHY (Ready for Release)', 'color:#30d158; font-weight:bold; font-size:16px; padding:8px; border: 2px solid #30d158; border-radius: 4px;');
          else console.log('%c⚠️ ISSUES DETECTED - CHECK TABLES', 'color:#ff453a; font-weight:bold; font-size:16px; padding:8px; border: 2px solid #ff453a; border-radius: 4px;');

          console.groupEnd();
          return 'Diagnosis Complete';
        };
      })();

      log('Deck 0 mini (V18.0 UX Optimization) Loaded.', 'success');

    })();
  })();
})();
