/**
 * 台球游戏移动端适配脚本
 * 为压缩混淆的游戏代码添加移动端支持
 */

(function() {
    'use strict';
    
    // 等待游戏初始化完成
    function waitForGameInit(callback) {
        if (window.BallGame) {
            callback();
        } else {
            setTimeout(() => waitForGameInit(callback), 100);
        }
    }
    
    // 移动端适配初始化
    function initMobileAdaptation() {
        waitForGameInit(() => {
            console.log('开始初始化台球游戏移动端适配');
            
            // 添加移动端方法和属性
            addMobileMethods();
            
            // 优化触摸控制
            optimizeTouchControls();
            
            // 添加游戏状态管理
            addGameStateManagement();
            
            console.log('台球游戏移动端适配完成');
        });
    }
    
    // 添加移动端方法
    function addMobileMethods() {
        if (!window.BallGame.mobileAdapted) {
            
            // 重置游戏方法
            BallGame.reset = function() {
                // 重新创建所有球
                BallGame.Ball.balls = [];
                BallGame.Ball.createBalls();
                
                // 重置玩家状态
                if (BallGame.player1) {
                    BallGame.player1.init();
                }
                if (BallGame.player2) {
                    BallGame.player2.init();
                }
                
                // 重置游戏状态
                BallGame.power = 1;
                BallGame.powerV = 0.4;
                BallGame.canShot = true;
                BallGame.isDown = false;
                BallGame.paused = false;
                
                // 隐藏胜利/失败提示
                if (BallGame.winTxt) {
                    BallGame.winTxt.alpha = 0;
                }
                if (BallGame.loseTxt) {
                    BallGame.loseTxt.alpha = 0;
                }
                
                // 重置控制器
                if (window.orientationManager) {
                    orientationManager.hideWarning();
                }
            };
            
            // 更新力度方法
            BallGame.updatePower = function(level) {
                // 将0-1的范围映射到游戏的力度范围(1-27)
                BallGame.power = 1 + (level * 26);
            };
            
            // 移动端击球方法
            BallGame.mobileShoot = function(targetX, targetY) {
                if (BallGame.canShot && !BallGame.paused) {
                    // 计算击球方向和力度
                    const whiteBall = BallGame.whiteBall;
                    if (whiteBall) {
                        const dx = targetX - whiteBall.x;
                        const dy = targetY - whiteBall.y;
                        const angle = Math.atan2(dy, dx);
                        
                        // 设置白球速度
                        whiteBall.v.x = Math.cos(angle) * BallGame.power;
                        whiteBall.v.y = Math.sin(angle) * BallGame.power;
                        
                        // 隐藏控制器
                        BallGame.cue.visible = false;
                        BallGame.line.visible = false;
                        BallGame.point.visible = false;
                        
                        // 重置状态
                        BallGame.canShot = false;
                        BallGame.power = 1;
                        BallGame.powerV = 0.4;
                        
                        // 开始游戏循环
                        BallGame.loop = BallGame.Ball.update;
                    }
                }
            };
            
            // 智能击球方法（自动计算角度和力度）
            BallGame.smartShoot = function() {
                if (BallGame.canShot && !BallGame.paused) {
                    const whiteBall = BallGame.whiteBall;
                    const balls = BallGame.Ball.balls;
                    
                    // 找到最近的球洞
                    let nearestHole = null;
                    let minDistance = Infinity;
                    
                    for (let i = 0; i < BallGame.holePoint.length; i++) {
                        const hole = BallGame.holePoint[i];
                        const dx = hole[0] + BallGame.offset.x - whiteBall.x;
                        const dy = hole[1] + BallGame.offset.y - whiteBall.y;
                        const distance = Math.sqrt(dx * dx + dy * dy);
                        
                        if (distance < minDistance) {
                            minDistance = distance;
                            nearestHole = hole;
                        }
                    }
                    
                    if (nearestHole) {
                        // 计算击球角度
                        const targetX = nearestHole[0] + BallGame.offset.x;
                        const targetY = nearestHole[1] + BallGame.offset.y;
                        
                        // 设置合适的力度（根据距离调整）
                        const distance = minDistance;
                        const power = Math.min(Math.max(distance / 30, 5), 20);
                        
                        // 执行击球
                        whiteBall.v.reset(targetX - whiteBall.x, targetY - whiteBall.y);
                        whiteBall.v.setLength(power);
                        
                        // 隐藏控制器
                        BallGame.cue.visible = false;
                        BallGame.line.visible = false;
                        BallGame.point.visible = false;
                        
                        // 重置状态
                        BallGame.canShot = false;
                        BallGame.power = 1;
                        BallGame.powerV = 0.4;
                        
                        // 开始游戏循环
                        BallGame.loop = BallGame.Ball.update;
                    }
                }
            };
            
            // 暂停/恢复游戏
            BallGame.togglePause = function() {
                BallGame.paused = !BallGame.paused;
                return BallGame.paused;
            };
            
            // 获取游戏状态
            BallGame.getGameState = function() {
                return {
                    paused: BallGame.paused,
                    canShot: BallGame.canShot,
                    power: BallGame.power,
                    score1: BallGame.player1 ? BallGame.player1.score : 0,
                    score2: BallGame.player2 ? BallGame.player2.score : 0,
                    ballsRemaining: BallGame.Ball.balls ? BallGame.Ball.balls.length : 0
                };
            };
            
            // 标记已适配
            BallGame.mobileAdapted = true;
        }
    }
    
    // 优化触摸控制
    function optimizeTouchControls() {
        // 改进现有的触摸事件处理
        if (BallGame.stage && BallGame.stage.container) {
            const container = BallGame.stage.container;
            
            // 优化触摸开始事件
            const originalTouchStart = container.ontouchstart;
            container.ontouchstart = function(e) {
                if (originalTouchStart) {
                    originalTouchStart.call(this, e);
                }
                
                // 如果游戏暂停，阻止操作
                if (BallGame.paused) {
                    e.preventDefault();
                    return;
                }
                
                // 更新鼠标位置
                if (e.touches && e.touches[0]) {
                    BallGame.mouse.x = e.touches[0].clientX;
                    BallGame.mouse.y = e.touches[0].clientY;
                }
                
                BallGame.isDown = true;
                e.preventDefault();
            };
            
            // 优化触摸移动事件
            const originalTouchMove = container.ontouchmove;
            container.ontouchmove = function(e) {
                if (originalTouchMove) {
                    originalTouchMove.call(this, e);
                }
                
                // 如果游戏暂停，阻止操作
                if (BallGame.paused) {
                    e.preventDefault();
                    return;
                }
                
                // 更新鼠标位置
                if (e.touches && e.touches[0]) {
                    BallGame.mouse.x = e.touches[0].clientX;
                    BallGame.mouse.y = e.touches[0].clientY;
                }
                
                e.preventDefault();
            };
            
            // 优化触摸结束事件
            const originalTouchEnd = container.ontouchend;
            container.ontouchend = function(e) {
                if (originalTouchEnd) {
                    originalTouchEnd.call(this, e);
                }
                
                // 如果游戏暂停，阻止操作
                if (BallGame.paused) {
                    e.preventDefault();
                    return;
                }
                
                // 执行击球
                if (BallGame.isDown && BallGame.canShot) {
                    const whiteBall = BallGame.whiteBall;
                    if (whiteBall && whiteBall.isDown) {
                        BallGame.shoot(BallGame.mouse.x, BallGame.mouse.y);
                    }
                }
                
                BallGame.isDown = false;
                e.preventDefault();
            };
        }
    }
    
    // 添加游戏状态管理
    function addGameStateManagement() {
        // 游戏循环改进
        const originalLoop = BallGame.loop;
        BallGame.loop = function() {
            // 如果游戏暂停，跳过更新
            if (BallGame.paused) {
                return;
            }
            
            // 执行原始循环
            if (originalLoop) {
                originalLoop.call(this);
            }
        };
        
        // 添加自动保存功能
        let autoSaveInterval;
        BallGame.startAutoSave = function() {
            if (autoSaveInterval) {
                clearInterval(autoSaveInterval);
            }
            
            autoSaveInterval = setInterval(() => {
                if (!BallGame.paused) {
                    const state = BallGame.getGameState();
                    localStorage.setItem('taiqiu_game_state', JSON.stringify(state));
                }
            }, 5000); // 每5秒保存一次
        };
        
        BallGame.stopAutoSave = function() {
            if (autoSaveInterval) {
                clearInterval(autoSaveInterval);
                autoSaveInterval = null;
            }
        };
        
        // 启动自动保存
        BallGame.startAutoSave();
    }
    
    // 页面加载完成后初始化
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', initMobileAdaptation);
    } else {
        initMobileAdaptation();
    }
    
})();