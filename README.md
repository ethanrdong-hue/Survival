# Survival
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Apocalypse Outpost</title>
  <style>
    :root {
      --bg-1: #0d1d1a;
      --bg-2: #172f2a;
      --panel: rgba(5, 12, 15, 0.72);
      --panel-border: rgba(128, 220, 180, 0.5);
      --text: #eaf7f3;
      --muted: #9fc3b6;
      --green: #62d39b;
      --gold: #f6c76a;
      --scrap: #d7d7d7;
      --wood: #c98c43;
      --metal: #6b7d8e;
      --danger: #f04d57;
      --warning: #ffb657;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      background: radial-gradient(circle at top, #1a312f 0%, var(--bg-1) 45%, #0b1417 100%);
      color: var(--text);
      font-family: Arial, Helvetica, sans-serif;
      height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
    }

    .game-shell {
      width: min(1100px, 96vw);
      height: min(690px, 92vh);
      background: rgba(10, 16, 17, 0.9);
      border: 2px solid var(--panel-border);
      border-radius: 12px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.45);
      overflow: hidden;
      position: relative;
    }

    #hud {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 120px;
      background: linear-gradient(to bottom, rgba(10, 15, 15, 0.9), rgba(10, 15, 15, 0.45));
      border-bottom: 1px solid rgba(140, 220, 180, 0.4);
      z-index: 5;
      padding: 12px 18px;
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      gap: 20px;
    }

    .stats {
      display: flex;
      flex-wrap: wrap;
      gap: 10px 16px;
      font-size: 13px;
      color: var(--text);
    }

    .stat {
      background: var(--panel);
      border: 1px solid rgba(140, 220, 180, 0.25);
      border-radius: 8px;
      padding: 8px 10px;
      min-width: 110px;
      box-shadow: inset 0 0 9px rgba(255,255,255,0.02);
    }

    .stat .title {
      display: block;
      font-size: 10px;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 4px;
    }

    .stat .value {
      font-weight: 700;
      font-size: 18px;
    }

    .resource-wood { color: var(--wood); }
    .resource-scrap { color: var(--scrap); }
    .resource-metal { color: var(--metal); }
    .resource-food { color: var(--green); }

    .meta {
      display: flex;
      flex-direction: column;
      align-items: flex-end;
      gap: 8px;
      min-width: 300px;
    }

    .weapon-box {
      background: rgba(17, 29, 31, 0.8);
      border: 1px solid rgba(255, 190, 100, 0.35);
      border-radius: 8px;
      padding: 8px 12px;
      color: var(--warning);
      text-align: right;
      font-weight: 700;
      letter-spacing: 0.05em;
    }

    .message {
      font-size: 12px;
      color: var(--muted);
      text-align: right;
      min-height: 18px;
      max-width: 420px;
      line-height: 1.4;
    }

    .controls-box {
      position: absolute;
      bottom: 14px;
      left: 14px;
      z-index: 6;
      background: rgba(8, 14, 15, 0.78);
      border: 1px solid rgba(140, 220, 180, 0.25);
      border-radius: 10px;
      padding: 8px 10px;
      font-size: 12px;
      color: var(--muted);
      line-height: 1.6;
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }

    .controls-box strong { color: var(--text); }

    #game {
      position: absolute;
      left: 0;
      top: 120px;
      width: 100%;
      height: calc(100% - 120px);
      display: block;
      background: linear-gradient(#1a2b2b, #13231d);
      cursor: crosshair;
    }

    .buttons {
      position: absolute;
      right: 16px;
      bottom: 16px;
      display: flex;
      flex-direction: column;
      gap: 8px;
      z-index: 6;
    }

    button {
      border: 1px solid rgba(150, 220, 180, 0.4);
      background: rgba(18, 31, 30, 0.85);
      color: var(--text);
      padding: 9px 12px;
      border-radius: 8px;
      font-size: 12px;
      cursor: pointer;
      text-align: left;
      min-width: 170px;
      transition: transform 0.12s ease, border-color 0.12s ease, box-shadow 0.12s ease;
    }

    button:hover {
      transform: translateY(-1px);
      border-color: rgba(160, 230, 180, 0.8);
      box-shadow: 0 8px 20px rgba(0,0,0,0.2);
    }

    button:active { transform: translateY(0); }

    button .cost {
      color: var(--gold);
      font-weight: 700;
    }

    @media (max-width: 860px) {
      .game-shell { width: 96vw; height: 90vh; }
      #hud { height: 150px; }
      #game { top: 150px; height: calc(100% - 150px); }
      .meta { min-width: 180px; }
      .buttons { right: 8px; bottom: 8px; }
    }
  </style>
</head>
<body>
  <div class="game-shell">
    <div id="hud">
      <div class="stats">
        <div class="stat">
          <span class="title">Health</span>
          <span class="value" id="health">100 / 100</span>
        </div>
        <div class="stat">
          <span class="title">Wood</span>
          <span class="value resource-wood" id="wood">0</span>
        </div>
        <div class="stat">
          <span class="title">Scrap</span>
          <span class="value resource-scrap" id="scrap">0</span>
        </div>
        <div class="stat">
          <span class="title">Metal</span>
          <span class="value resource-metal" id="metal">0</span>
        </div>
        <div class="stat">
          <span class="title">Food</span>
          <span class="value resource-food" id="food">0</span>
        </div>
      </div>

      <div class="meta">
        <div class="weapon-box" id="weapon-box">Weapon: Rusty Pipe</div>
        <div class="message" id="message">Scavenge the ruins. Survive the night.</div>
      </div>
    </div>

    <canvas id="game" width="1100" height="570"></canvas>

    <div class="controls-box">
      <strong>Controls:</strong><br>
      WASD / Arrows = Move<br>
      E = Gather loot / Open chest<br>
      Space / Click = Attack<br>
      1 = Wall, 2 = Turret, 3 = Camp
    </div>

    <div class="buttons">
      <button data-build="wall">
        Build Wall <span class="cost">(Wood 18)</span>
      </button>
      <button data-build="turret">
        Build Turret <span class="cost">(Scrap 20 + Metal 12)</span>
      </button>
      <button data-build="camp">
        Build Camp <span class="cost">(Wood 12 + Food 8 + Metal 6)</span>
      </button>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const hud = {
      health: document.getElementById("health"),
      wood: document.getElementById("wood"),
      scrap: document.getElementById("scrap"),
      metal: document.getElementById("metal"),
      food: document.getElementById("food"),
      weaponBox: document.getElementById("weapon-box"),
      message: document.getElementById("message")
    };

    const state = {
      time: 0,
      wave: 1,
      messageTimer: 0,
      messageText: "Scavenge the ruins. Survive the night.",
      score: 0,
      resources: {
        wood: 30,
        scrap: 24,
        metal: 12,
        food: 14
      },
      weapons: [
        { name: "Rusty Pipe", damage: 13, range: 72, cooldown: 0.66, color: "#d6d1b0" },
        { name: "Hunting Rifle", damage: 22, range: 170, cooldown: 0.45, color: "#b9d3ff" },
        { name: "Sawblade Cannon", damage: 31, range: 195, cooldown: 0.32, color: "#d0a1ff" },
        { name: "Inferno Carbine", damage: 46, range: 220, cooldown: 0.22, color: "#ff9d6c" }
      ],
      weaponIndex: 0,
      player: {
        x: canvas.width / 2,
        y: canvas.height / 2,
        radius: 16,
        speed: 190,
        hp: 100,
        maxHp: 100,
        facingX: 1,
        facingY: 0,
        attackCooldown: 0
      },
      buildCosts: {
        wall: { wood: 18 },
        turret: { scrap: 20, metal: 12 },
        camp: { wood: 12, food: 8, metal: 6 }
      },
      enemies: [],
      buildings: [],
      resourceNodes: [],
      chests: [],
      particles: [],
      mouse: {
        x: canvas.width / 2,
        y: canvas.height / 2,
        down: false
      }
    };

    const keys = {};
    let lastTime = 0;

    function clamp(value, min, max) {
      return Math.min(Math.max(value, min), max);
    }

    function dist(a, b) {
      return Math.hypot(a.x - b.x, a.y - b.y);
    }

    function setMessage(text, duration = 2.4) {
      state.messageText = text;
      state.messageTimer = duration;
      hud.message.textContent = text;
    }

    function addParticle(x, y, color, radius = 3, life = 0.5, speed = 50) {
      state.particles.push({
        x, y, color, radius, life, maxLife: life, vx: (Math.random() - 0.5) * speed, vy: (Math.random() - 0.5) * speed
      });
    }

    function gainResource(type, amount) {
      state.resources[type] += amount;
      if (type === "wood") hud.wood.textContent = Math.floor(state.resources.wood);
      if (type === "scrap") hud.scrap.textContent = Math.floor(state.resources.scrap);
      if (type === "metal") hud.metal.textContent = Math.floor(state.resources.metal);
      if (type === "food") hud.food.textContent = Math.floor(state.resources.food);
    }

    function canAfford(cost) {
      for (const k in cost) {
        if ((state.resources[k] || 0) < cost[k]) return false;
      }
      return true;
    }

    function spend(cost) {
      for (const k in cost) {
        state.resources[k] -= cost[k];
      }
      updateHud();
    }

    function updateHud() {
      hud.health.textContent = Math.ceil(state.player.hp) + " / " + state.player.maxHp;
      hud.wood.textContent = Math.floor(state.resources.wood);
      hud.scrap.textContent = Math.floor(state.resources.scrap);
      hud.metal.textContent = Math.floor(state.resources.metal);
      hud.food.textContent = Math.floor(state.resources.food);
      hud.weaponBox.textContent = "Weapon: " + state.weapons[state.weaponIndex].name;
    }

    function spawnResourceNode(type, x, y) {
      state.resourceNodes.push({ type, x, y, amount: 26 + Math.random() * 25, radius: 18 });
    }

    function createInitialWorld() {
      const types = ["wood", "scrap", "metal", "food"];
      for (let i = 0; i < 18; i++) {
        const type = types[Math.floor(Math.random() * types.length)];
        const x = 60 + Math.random() * (canvas.width - 120);
        const y = 150 + Math.random() * (canvas.height - 220);
        spawnResourceNode(type, x, y);
      }
      spawnChest(200, 240);
      spawnChest(850, 360);
      spawnChest(700, 170);
    }

    function spawnChest(x, y) {
      state.chests.push({
        x, y,
        radius: 16,
        opened: false,
        reward: Math.random() < 0.65 ? "weapon" : "resource"
      });
    }

    function openChest(chest) {
      if (chest.opened) return;
      chest.opened = true;

      if (chest.reward === "weapon") {
        if (state.weaponIndex < state.weapons.length - 1) {
          state.weaponIndex++;
          setMessage("Weapon upgraded: " + state.weapons[state.weaponIndex].name + "!");
          updateHud();
        } else {
          gainResource("scrap", 20);
          gainResource("metal", 12);
          setMessage("Chest found supplies: +20 scrap, +12 metal");
        }
      } else {
        const loot = [
          ["wood", 16 + Math.random() * 12],
          ["scrap", 14 + Math.random() * 14],
          ["metal", 8 + Math.random() * 8],
          ["food", 12 + Math.random() * 10]
        ];
        const choice = loot[Math.floor(Math.random() * loot.length)];
        gainResource(choice[0], choice[1]);
        setMessage("Loot chest: +" + Math.floor(choice[1]) + " " + choice[0]);
      }
    }

    function gatherNearbyResource() {
      let nearest = null;
      for (const node of state.resourceNodes) {
        if (node.amount <= 0) continue;
        const d = dist(state.player, node);
        if (d < 45) {
          if (!nearest || d < nearest.d) nearest = { node, d };
        }
      }
      if (nearest) {
        const amount = 8 + Math.floor(Math.random() * 7);
        gainResource(nearest.node.type, amount);
        nearest.node.amount -= amount;
        if (nearest.node.amount <= 0) {
          nearest.node.amount = 0;
        }
        setMessage("Gathered " + amount + " " + nearest.node.type + " from the ruins.");
      } else {
        const nearbyChest = state.chests.find(c => !c.opened && dist(state.player, c) < 42);
        if (nearbyChest) {
          openChest(nearbyChest);
        } else {
          setMessage("No resources or chests nearby.");
        }
      }
    }

    function buildStructure(type) {
      const cost = state.buildCosts[type];
      if (!cost) return;

      if (!canAfford(cost)) {
        setMessage("Need more materials for that structure.");
        return;
      }

      const offset = 30;
      const x = clamp(state.player.x + offset, 40, canvas.width - 60);
      const y = clamp(state.player.y + offset, 50, canvas.height - 60);

      spend(cost);

      if (type === "wall") {
        state.buildings.push({
          x, y, width: 26, height: 26, hp: 90, type: "wall", color: "#8d6d4c"
        });
        setMessage("Barricade built.");
      }

      if (type === "turret") {
        state.buildings.push({
          x, y, width: 22, height: 22, hp: 70, type: "turret", color: "#839bb0", fireCooldown: 0.9
        });
        setMessage("Turret deployed.");
      }

      if (type === "camp") {
        state.buildings.push({
          x, y, width: 32, height: 32, hp: 120, type: "camp", color: "#5d9b67", regen: 0.6
        });
        setMessage("Supply camp established.");
      }
    }

    function attack() {
      if (state.player.attackCooldown > 0) return;

      const weapon = state.weapons[state.weaponIndex];
      const target = state.enemies
        .filter(e => dist(state.player, e) <= weapon.range + e.radius + 5)
        .sort((a, b) => dist(state.player, a) - dist(state.player, b))[0];

      if (!target) {
        setMessage("No target in range.");
        return;
      }

      state.player.attackCooldown = weapon.cooldown;
      target.hp -= weapon.damage;

      addParticle(target.x, target.y, weapon.color, 4, 0.4, 80);

      if (target.hp <= 0) {
        target.dead = true;
        state.score += 1;
        const dropChance = Math.random();

        if (dropChance < 0.28) {
          const dropType = ["scrap", "metal", "wood", "food"][Math.floor(Math.random() * 4)];
          gainResource(dropType, 7 + Math.floor(Math.random() * 10));
          setMessage("Zombie down! +" + (7 + Math.floor(Math.random() * 10)) + " " + dropType);
        } else {
          setMessage("Zombie eliminated.");
        }

        if (Math.random() < 0.2) {
          const chestX = clamp(target.x + (Math.random() - 0.5) * 50, 30, canvas.width - 30);
          const chestY = clamp(target.y + (Math.random() - 0.5) * 50, 40, canvas.height - 40);
          spawnChest(chestX, chestY);
          setMessage("A weapon cache dropped!");
        }
      }
    }

    function spawnEnemy() {
      const edge = Math.floor(Math.random() * 4);
      let x, y;
      if (edge === 0) { x = Math.random() * canvas.width; y = -20; }
      if (edge === 1) { x = canvas.width + 20; y = Math.random() * canvas.height; }
      if (edge === 2) { x = Math.random() * canvas.width; y = canvas.height + 20; }
      if (edge === 3) { x = -20; y = Math.random() * canvas.height; }

      const hp = 24 + state.wave * 7;
      state.enemies.push({
        x, y,
        radius: 14,
        maxHp: hp,
        hp: hp,
        speed: 38 + state.wave * 4,
        attackCooldown: 0,
        color: "#e44e4e",
        dead: false
      });
    }

    function update(dt) {
      state.time += dt;

      if (state.messageTimer > 0) {
        state.messageTimer -= dt;
        if (state.messageTimer <= 0) {
          hud.message.textContent = state.messageText;
        }
      }

      state.player.attackCooldown = Math.max(0, state.player.attackCooldown - dt);

      const moveX = (keys["d"] || keys["arrowright"] ? 1 : 0) - (keys["a"] || keys["arrowleft"] ? 1 : 0);
      const moveY = (keys["s"] || keys["arrowdown"] ? 1 : 0) - (keys["w"] || keys["arrowup"] ? 1 : 0);

      if (moveX !== 0 || moveY !== 0) {
        const len = Math.hypot(moveX, moveY) || 1;
        const dx = (moveX / len) * state.player.speed * dt;
        const dy = (moveY / len) * state.player.speed * dt;
        state.player.x = clamp(state.player.x + dx, 18, canvas.width - 18);
        state.player.y = clamp(state.player.y + dy, 18, canvas.height - 18);
      }

      if (state.mouse.x !== undefined) {
        const dx = state.mouse.x - state.player.x;
        const dy = state.mouse.y - state.player.y;
        const ang = Math.atan2(dy, dx);
        state.player.facingX = Math.cos(ang);
        state.player.facingY = Math.sin(ang);
      }

      // Enemy spawn control
      if (state.time > 0 && Math.floor(state.time * 3) > Math.floor((state.time - dt) * 3)) {
        // Roughly 3 per second scaling with wave
        if (state.enemies.length < 10 + state.wave * 2) {
          spawnEnemy();
        }
      }

      // Gradually increase wave based on time
      const nextWave = 1 + Math.floor(state.time / 25);
      if (nextWave > state.wave) {
        state.wave = nextWave;
        setMessage("Wave " + state.wave + " incoming! The city is collapsing.");
      }

      // Update buildings
      for (const b of state.buildings) {
        if (b.type === "camp") {
          b.regen = (b.regen || 0.6) + 0.01;
          if (Math.random() < 0.025) {
            gainResource("food", 1);
          }
        }

        if (b.type === "turret") {
          b.fireCooldown = (b.fireCooldown || 0.8) - dt;
          if (b.fireCooldown <= 0) {
            const target = state.enemies
              .filter(e => dist(b, e) < 180)
              .sort((a, b) => dist(b, a) - dist(b, a))[0];

            if (target) {
              b.fireCooldown = 0.85;
              const bullet = {
                x: b.x,
                y: b.y,
                dx: (target.x - b.x),
                dy: (target.y - b.y),
                speed: 260
              };
              const mag = Math.hypot(bullet.dx, bullet.dy) || 1;
              bullet.dx = (bullet.dx / mag) * bullet.speed;
              bullet.dy = (bullet.dy / mag) * bullet.speed;

              const turretShot = {
                x: b.x,
                y: b.y,
                dx: bullet.dx,
                dy: bullet.dy,
                life: 1.1,
                color: "#99d1ff"
              };

              state.particles.push({
                ...turretShot,
                radius: 3,
                maxLife: 1.1,
                isBullet: true
              });

              target.hp -= 16 + state.wave * 1.5;
              addParticle(target.x, target.y, "#9dd9ff", 3, 0.35, 50);

              if (target.hp <= 0) {
                target.dead = true;
                setMessage("Turret scored!");
              }
            }
          }
        }
      }

      // Update projectiles/particles
      for (const p of state.particles) {
        p.x += p.vx * dt;
        p.y += p.vy * dt;
        p.life -= dt;

        if (p.isBullet) {
          p.x += p.dx * dt;
          p.y += p.dy * dt;
        }
      }
      state.particles = state.particles.filter(p => p.life > 0);

      // Enemies move toward player
      for (const e of state.enemies) {
        if (e.dead) continue;

        const dx = state.player.x - e.x;
        const dy = state.player.y - e.y;
        const len = Math.hypot(dx, dy) || 1;
        e.x += (dx / len) * e.speed * dt;
        e.y += (dy / len) * e.speed * dt;

        if (dist(state.player, e) < state.player.radius + e.radius + 4) {
          e.attackCooldown -= dt;
          if (e.attackCooldown <= 0) {
            state.player.hp -= 8 + state.wave * 1.2;
            e.attackCooldown = 1.2;
            addParticle(state.player.x, state.player.y, "#ff9a9a", 5, 0.45, 90);
          }
        }
      }

      state.enemies = state.enemies.filter(e => !e.dead && e.hp > 0);
      state.chests = state.chests.filter(c => !c.opened);
      state.resourceNodes = state.resourceNodes.filter(node => node.amount > 0);

      // Player damage clamp
      if (state.player.hp <= 0) {
        state.player.hp = state.player.maxHp;
        state.player.x = canvas.width / 2;
        state.player.y = canvas.height / 2;
        setMessage("You were overwhelmed. You return to camp.");
        gainResource("food", 3);
      }

      updateHud();
    }

    function drawBackground() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
      gradient.addColorStop(0, "#1a2a29");
      gradient.addColorStop(1, "#0f1b1a");
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Ground details
      for (let x = 0; x < canvas.width; x += 36) {
        for (let y = 0; y < canvas.height; y += 36) {
          ctx.fillStyle = (x + y) % 72 === 0 ? "rgba(255,255,255,0.02)" : "rgba(0,0,0,0.02)";
          ctx.fillRect(x, y, 36, 36);
        }
      }

      ctx.strokeStyle = "rgba(160, 220, 190, 0.12)";
      ctx.lineWidth = 1;
      for (let x = 0; x <= canvas.width; x += 100) {
        ctx.beginPath();
        ctx.moveTo(x, 0);
        ctx.lineTo(x, canvas.height);
        ctx.stroke();
      }
      for (let y = 0; y <= canvas.height; y += 100) {
        ctx.beginPath();
        ctx.moveTo(0, y);
        ctx.lineTo(canvas.width, y);
        ctx.stroke();
      }
    }

    function drawResourceNodes() {
      for (const node of state.resourceNodes) {
        let color = "#4d9d77";
        if (node.type === "wood") color = "#c98c43";
        if (node.type === "scrap") color = "#b8bec5";
        if (node.type === "metal") color = "#7d8ea1";
        if (node.type === "food") color = "#67d78d";

        ctx.beginPath();
        ctx.fillStyle = color;
        ctx.arc(node.x, node.y, node.radius, 0, Math.PI * 2);
        ctx.fill();

        ctx.beginPath();
        ctx.fillStyle = "rgba(0,0,0,0.35)";
        ctx.arc(node.x, node.y, node.radius * 0.5, 0, Math.PI * 2);
        ctx.fill();

        ctx.fillStyle = "rgba(255,255,255,0.6)";
        ctx.font = "10px Arial";
        ctx.fillText(node.type[0].toUpperCase(), node.x - 5, node.y + 4);
      }
    }

    function drawBuildings() {
      for (const b of state.buildings) {
        ctx.fillStyle = b.color;
        ctx.fillRect(b.x - b.width / 2, b.y - b.height / 2, b.width, b.height);

        if (b.type === "turret") {
          ctx.beginPath();
          ctx.strokeStyle = "#eefaff";
          ctx.lineWidth = 2;
          ctx.moveTo(b.x, b.y);
          const aim = state.enemies[0];
          if (aim) {
            const ang = Math.atan2(aim.y - b.y, aim.x - b.x);
            ctx.lineTo(b.x + Math.cos(ang) * 18, b.y + Math.sin(ang) * 18);
          }
          ctx.stroke();
        }
      }
    }

    function drawChests() {
      for (const c of state.chests) {
        ctx.fillStyle = "#7a5634";
        ctx.fillRect(c.x - 12, c.y - 12, 24, 24);

        ctx.fillStyle = "#d8b77d";
        ctx.fillRect(c.x - 8, c.y - 2, 16, 6);

        ctx.fillStyle = "#f0d190";
        ctx.fillRect(c.x - 4, c.y - 10, 8, 10);

        ctx.fillStyle = "rgba(0,0,0,0.25)";
        ctx.fillRect(c.x - 10, c.y + 10, 20, 4);
      }
    }

    function drawEnemies() {
      for (const e of state.enemies) {
        ctx.beginPath();
        ctx.fillStyle = e.color;
        ctx.arc(e.x, e.y, e.radius, 0, Math.PI * 2);
        ctx.fill();

        // Eyes
        ctx.fillStyle = "#1a1111";
        ctx.fillRect(e.x - 4, e.y - 4, 3, 3);
        ctx.fillRect(e.x + 1, e.y - 4, 3, 3);

        // HP bar
        const hpWidth = 22;
        ctx.fillStyle = "rgba(0,0,0,0.3)";
        ctx.fillRect(e.x - hpWidth / 2, e.y - 22, hpWidth, 5);
        ctx.fillStyle = "#80ef9f";
        ctx.fillRect(e.x - hpWidth / 2, e.y - 22, (e.hp / e.maxHp) * hpWidth, 5);
      }
    }

    function drawPlayer() {
      const p = state.player;

      ctx.beginPath();
      ctx.fillStyle = "#70b1ff";
      ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
      ctx.fill();

      // Weapon line
      ctx.strokeStyle = "#e9f4ff";
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.moveTo(p.x, p.y);
      ctx.lineTo(p.x + p.facingX * 18, p.y + p.facingY * 18);
      ctx.stroke();

      // Health ring
      ctx.beginPath();
      ctx.strokeStyle = "#9de8b7";
      ctx.lineWidth = 4;
      ctx.arc(p.x, p.y, p.radius + 8, -Math.PI / 2, (-Math.PI / 2) + (state.player.hp / state.player.maxHp) * (Math.PI * 2));
      ctx.stroke();
    }

    function drawParticles() {
      for (const p of state.particles) {
        ctx.beginPath();
        ctx.fillStyle = p.color;
        ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
        ctx.fill();
      }
    }

    function render() {
      drawBackground();
      drawResourceNodes();
      drawBuildings();
      drawChests();
      drawEnemies();
      drawParticles();
      drawPlayer();
    }

    function loop(ts) {
      const dt = Math.min((ts - lastTime) / 1000 || 0.016, 0.033);
      lastTime = ts;
      update(dt);
      render();
      requestAnimationFrame(loop);
    }

    function onPointerMove(e) {
      const rect = canvas.getBoundingClientRect();
      state.mouse.x = ((e.clientX - rect.left) / rect.width) * canvas.width;
      state.mouse.y = ((e.clientY - rect.top) / rect.height) * canvas.height;
    }

    canvas.addEventListener("mousemove", onPointerMove);
    canvas.addEventListener("mousedown", () => {
      state.mouse.down = true;
      attack();
    });

    document.addEventListener("keydown", (e) => {
      const key = e.key.toLowerCase();
      keys[key] = true;

      if (key === "e") gatherNearbyResource();
      if (key === " " || key === "spacebar") attack();

      if (key === "1") buildStructure("wall");
      if (key === "2") buildStructure("turret");
      if (key === "3") buildStructure("camp");
    });

    document.addEventListener("keyup", (e) => {
      keys[e.key.toLowerCase()] = false;
    });

    document.querySelectorAll("[data-build]").forEach((btn) => {
      btn.addEventListener("click", () => {
        const type = btn.dataset.build;
        buildStructure(type);
      });
    });

    function init() {
      createInitialWorld();
      updateHud();
      setMessage("Scavenge the ruins. Survive the night.");
      requestAnimationFrame(loop);
    }

    init();
  </script>
</body>
</html>
