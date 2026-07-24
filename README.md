<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Lost Temple - Adventure Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Courier New', monospace;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            color: #eee;
            padding: 20px;
        }

        .container {
            max-width: 700px;
            width: 100%;
            background: rgba(0, 0, 0, 0.7);
            border: 3px solid #ffa500;
            border-radius: 10px;
            padding: 40px;
            box-shadow: 0 0 30px rgba(255, 165, 0, 0.5);
        }

        h1 {
            color: #ffa500;
            text-align: center;
            margin-bottom: 20px;
            text-shadow: 0 0 10px rgba(255, 165, 0, 0.8);
            font-size: 2em;
        }

        .game-info {
            display: flex;
            justify-content: space-between;
            margin-bottom: 20px;
            padding: 10px;
            background: rgba(255, 165, 0, 0.1);
            border-radius: 5px;
            font-size: 0.9em;
        }

        .health, .inventory {
            color: #ffa500;
        }

        .story-text {
            background: rgba(255, 255, 255, 0.05);
            border-left: 4px solid #ffa500;
            padding: 20px;
            margin: 20px 0;
            min-height: 120px;
            line-height: 1.6;
            font-size: 1.1em;
            border-radius: 5px;
        }

        .choices {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin: 20px 0;
        }

        .choice-btn {
            background: linear-gradient(135deg, #ffa500 0%, #ff8c00 100%);
            color: #000;
            border: 2px solid #ffa500;
            padding: 12px 20px;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            font-family: 'Courier New', monospace;
        }

        .choice-btn:hover {
            background: linear-gradient(135deg, #ffb333 0%, #ff9900 100%);
            transform: translateX(5px);
            box-shadow: 0 0 15px rgba(255, 165, 0, 0.8);
        }

        .choice-btn:active {
            transform: translateX(3px);
        }

        .game-over {
            text-align: center;
            color: #ff4444;
            font-size: 1.5em;
            font-weight: bold;
            margin: 20px 0;
        }

        .game-won {
            text-align: center;
            color: #44ff44;
            font-size: 1.5em;
            font-weight: bold;
            margin: 20px 0;
        }

        .restart-btn {
            display: block;
            margin: 20px auto;
            background: #44ff44;
            color: #000;
            border: 2px solid #44ff44;
            padding: 12px 30px;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            font-family: 'Courier New', monospace;
        }

        .restart-btn:hover {
            background: #66ff66;
            box-shadow: 0 0 15px rgba(68, 255, 68, 0.8);
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🏛️ THE LOST TEMPLE 🏛️</h1>
        
        <div class="game-info">
            <div class="health">❤️ Health: <span id="health">100</span>/100</div>
            <div class="inventory">🎒 Items: <span id="items">0</span></div>
        </div>

        <div id="storyContainer">
            <div class="story-text" id="story"></div>
            <div class="choices" id="choices"></div>
        </div>

        <div id="gameOverContainer" class="hidden">
            <div class="game-over" id="gameOverText"></div>
            <button class="restart-btn" onclick="location.reload()">Play Again</button>
        </div>
    </div>

    <script>
        const game = {
            health: 100,
            items: [],
            currentScene: 'start',

            scenes: {
                start: {
                    text: "You wake up in front of an ancient, mysterious temple. The air is thick with secrets. You notice three paths before you:\n\n1. A dark cave entrance on your left\n2. A worn stone staircase leading up to the temple entrance\n3. A narrow path disappearing into dense jungle on your right",
                    choices: [
                        { text: "🔦 Enter the dark cave", next: 'cave' },
                        { text: "🏛️ Climb the temple stairs", next: 'stairs' },
                        { text: "🌿 Take the jungle path", next: 'jungle' }
                    ]
                },

                cave: {
                    text: "You enter the dark cave. Your eyes adjust slowly. Suddenly, you hear a low growl! A mysterious creature emerges from the shadows. It doesn't seem immediately hostile, just curious.\n\nWhat do you do?",
                    choices: [
                        { text: "⚔️ Attack the creature", next: 'caveAttack' },
                        { text: "🤝 Try to befriend it", next: 'caveFriend' },
                        { text: "🏃 Run back outside", next: 'start' }
                    ]
                },

                caveAttack: {
                    text: "You bravely attack the creature! It fights back fiercely. You take heavy damage, but manage to defeat it. You find a golden key in its lair!",
                    choices: [
                        { text: "Continue exploring", next: 'goldenKey', damage: 30, items: 'key' }
                    ]
                },

                caveFriend: {
                    text: "The creature responds to your gentle approach. It's actually a guardian beast! It gives you an ancient map and shares wisdom about the temple. You gain a valuable ally!",
                    choices: [
                        { text: "Continue with new knowledge", next: 'templeInside', items: 'map' }
                    ]
                },

                stairs: {
                    text: "You climb the ancient stone stairs. At the top, you find the main temple entrance. There's a riddle carved into the door:\n\n'I have cities but no houses, forests but no trees, and water but no fish. What am I?'\n\nAnswers: Map / Book / Drawing",
                    choices: [
                        { text: "📍 Say 'Map'", next: 'riddleCorrect' },
                        { text: "📖 Say 'Book'", next: 'riddleWrong' },
                        { text: "🎨 Say 'Drawing'", next: 'riddleWrong' }
                    ]
                },

                riddleCorrect: {
                    text: "Correct! The temple door glows and slowly opens. You step inside and discover a beautiful chamber filled with treasures and ancient artifacts. The temple seems to recognize you as worthy!",
                    choices: [
                        { text: "Enter the inner chamber", next: 'templeInside', items: 'artifact' }
                    ]
                },

                riddleWrong: {
                    text: "Wrong! The temple shakes violently. You lose your footing and tumble down the stairs, taking damage.",
                    choices: [
                        { text: "Try again", next: 'start', damage: 25 }
                    ]
                },

                jungle: {
                    text: "You venture into the jungle. The air is thick with mystery. After walking for a while, you find a hidden temple shrine. An old wise sage appears before you!\n\nThe sage offers you three choices for power:",
                    choices: [
                        { text: "🔮 Take the Staff of Wisdom", next: 'staffPath', items: 'staff' },
                        { text: "🗡️ Take the Sword of Courage", next: 'swordPath', items: 'sword' },
                        { text: "🛡️ Take the Shield of Protection", next: 'shieldPath', items: 'shield' }
                    ]
                },

                staffPath: {
                    text: "You take the Staff of Wisdom. The sage smiles and tells you the secret path to the temple's treasure vault. With wisdom as your guide, you navigate safely!",
                    choices: [
                        { text: "Head to the vault", next: 'treasureVault', items: 'keycard' }
                    ]
                },

                swordPath: {
                    text: "You take the Sword of Courage. The sage nods and warns you of dangerous guardians ahead. With courage and steel, you prepare for battle!",
                    choices: [
                        { text: "Face the guardians", next: 'guardianBattle', items: 'sword' }
                    ]
                },

                shieldPath: {
                    text: "You take the Shield of Protection. A powerful aura surrounds you. The sage reveals that true treasure lies in understanding, not taking. You feel inner peace.",
                    choices: [
                        { text: "Continue your journey", next: 'peacefulEnd', items: 'shield' }
                    ]
                },

                templeInside: {
                    text: "Inside the grand temple, you find yourself in a hall of mirrors. Your reflection seems... wrong. A choice appears before you:\n\nDo you trust your reflection or follow your instincts?",
                    choices: [
                        { text: "🪞 Trust the reflection", next: 'mirrorTrap', damage: 20 },
                        { text: "🧭 Follow your instincts", next: 'treasureVault' }
                    ]
                },

                mirrorTrap: {
                    text: "The mirrors were an illusion trap! You're disoriented and take damage. But you spot a hidden passage...",
                    choices: [
                        { text: "Take the passage", next: 'hiddenVault' }
                    ]
                },

                treasureVault: {
                    text: "🎉 SUCCESS! You've found the main treasure vault! Golden chests overflowing with riches, precious gems, and ancient artifacts surround you. The temple's magic recognizes your worthiness and grants you its blessing!\n\nYou've conquered the Lost Temple!",
                    choices: [
                        { text: "🏆 Claim Victory", next: 'victory' }
                    ]
                },

                hiddenVault: {
                    text: "🌟 AMAZING! You found a secret vault unknown even to the temple's guardians! Ancient treasures beyond imagination fill this chamber. You are truly blessed!",
                    choices: [
                        { text: "🏆 Celebrate Victory", next: 'victory' }
                    ]
                },

                guardianBattle: {
                    text: "You face the temple guardians with your sword! It's an epic battle, but your courage prevails! You defeat them and find the treasure room.",
                    choices: [
                        { text: "🏆 Claim Your Prize", next: 'victory', damage: 15 }
                    ]
                },

                peacefulEnd: {
                    text: "🕉️ You've achieved enlightenment! The temple reveals its greatest treasure: knowledge and wisdom. You leave the temple transformed, with peace in your heart.",
                    choices: [
                        { text: "🏆 Complete Your Journey", next: 'victory' }
                    ]
                },

                goldenKey: {
                    text: "Using the golden key, you unlock a secret door in the temple. Inside, you find the treasure chamber and claim the Lost Temple's greatest treasure!",
                    choices: [
                        { text: "🏆 Victory!", next: 'victory' }
                    ]
                },

                victory: {
                    text: null,
                    isEnding: true
                }
            },

            displayScene(sceneKey) {
                const scene = this.scenes[sceneKey];
                
                if (scene.isEnding) {
                    document.getElementById('storyContainer').classList.add('hidden');
                    const gameOverContainer = document.getElementById('gameOverContainer');
                    gameOverContainer.classList.remove('hidden');
                    document.getElementById('gameOverText').textContent = '🎊 YOU WON! 🎊\n\nYou have successfully conquered the Lost Temple!\nYour adventure will be remembered for generations!';
                    document.getElementById('gameOverText').classList.add('game-won');
                    return;
                }

                if (this.health <= 0) {
                    document.getElementById('storyContainer').classList.add('hidden');
                    const gameOverContainer = document.getElementById('gameOverContainer');
                    gameOverContainer.classList.remove('hidden');
                    document.getElementById('gameOverText').textContent = '💀 GAME OVER 💀\n\nYou have fallen... The temple claims another victim.';
                    document.getElementById('gameOverText').classList.remove('game-won');
                    return;
                }

                document.getElementById('story').textContent = scene.text;
                
                const choicesContainer = document.getElementById('choices');
                choicesContainer.innerHTML = '';
                
                scene.choices.forEach(choice => {
                    const button = document.createElement('button');
                    button.className = 'choice-btn';
                    button.textContent = choice.text;
                    button.onclick = () => this.makeChoice(choice);
                    choicesContainer.appendChild(button);
                });

                this.updateUI();
            },

            makeChoice(choice) {
                if (choice.damage) {
                    this.health -= choice.damage;
                    this.health = Math.max(0, this.health);
                }

                if (choice.items) {
                    this.items.push(choice.items);
                }

                this.currentScene = choice.next;
                this.displayScene(choice.next);
            },

            updateUI() {
                document.getElementById('health').textContent = Math.max(0, this.health);
                document.getElementById('items').textContent = this.items.length;
            },

            start() {
                this.displayScene('start');
            }
        };

        // Start the game when the page loads
        window.addEventListener('load', () => {
            game.start();
        });
    </script>
</body>
</html>
