<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OptimaTrade - Премиальная OTC платформа с контролем 30/70 и максимальной безопасностью</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
        }

        :root {
            --primary: #2962ff;
            --primary-dark: #1e4bd7;
            --primary-light: #768fff;
            --success: #00c853;
            --danger: #ff1744;
            --warning: #ff9100;
            --dark: #0a0e1c;
            --dark-secondary: #141b2b;
            --dark-tertiary: #1e2738;
            --light: #ffffff;
            --gray: #8a93a7;
            --gray-light: #b9c0d1;
            --border: rgba(255, 255, 255, 0.08);
            
            /* Безопасность */
            --security-low: #ff6b6b;
            --security-medium: #ffd93d;
            --security-high: #6bcf7f;
            --encryption: #9c27b0;
        }

        body {
            background: var(--dark);
            color: var(--light);
            overflow-x: hidden;
            min-height: 100vh;
        }

        /* Прелоадер с проверкой безопасности */
        .preloader {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--dark);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 9999;
            transition: opacity 0.5s;
        }

        .preloader.fade-out {
            opacity: 0;
            pointer-events: none;
        }

        .security-check {
            text-align: center;
        }

        .security-check h3 {
            color: var(--gray);
            margin: 20px 0;
            font-weight: 400;
        }

        .security-progress {
            width: 300px;
            height: 4px;
            background: var(--dark-tertiary);
            border-radius: 2px;
            overflow: hidden;
            margin: 20px 0;
        }

        .security-progress-bar {
            width: 0%;
            height: 100%;
            background: linear-gradient(90deg, var(--security-low), var(--security-medium), var(--security-high));
            animation: securityScan 2s forwards;
        }

        @keyframes securityScan {
            0% { width: 0%; }
            100% { width: 100%; }
        }

        .security-items {
            display: flex;
            gap: 20px;
            margin-top: 30px;
            color: var(--gray);
            font-size: 0.9rem;
        }

        .security-items i {
            margin-right: 8px;
        }

        .security-items .fa-check-circle {
            color: var(--security-high);
        }

        /* Навигация с индикатором безопасности */
        .navbar {
            background: rgba(10, 14, 28, 0.95);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid var(--border);
            padding: 16px 32px;
            position: sticky;
            top: 0;
            z-index: 100;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .logo {
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 1.8rem;
            font-weight: 700;
            background: linear-gradient(135deg, #fff 0%, var(--primary-light) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .logo i {
            font-size: 2.2rem;
            color: var(--primary);
            -webkit-text-fill-color: var(--primary);
        }

        .security-indicator {
            display: flex;
            align-items: center;
            gap: 12px;
            background: var(--dark-tertiary);
            padding: 8px 16px;
            border-radius: 40px;
            border: 1px solid var(--border);
            cursor: pointer;
            transition: all 0.3s;
        }

        .security-indicator:hover {
            border-color: var(--security-high);
        }

        .security-indicator i {
            color: var(--security-high);
            font-size: 1.2rem;
        }

        .security-level {
            display: flex;
            flex-direction: column;
        }

        .security-level span {
            font-size: 0.8rem;
            color: var(--gray);
        }

        .security-level strong {
            color: var(--security-high);
            font-size: 0.9rem;
        }

        .nav-links {
            display: flex;
            gap: 32px;
            align-items: center;
        }

        .nav-link {
            color: var(--gray);
            text-decoration: none;
            font-weight: 500;
            transition: color 0.3s;
            font-size: 1rem;
        }

        .nav-link:hover, .nav-link.active {
            color: var(--light);
        }

        .nav-link i {
            margin-right: 8px;
            font-size: 1rem;
        }

        .user-menu {
            display: flex;
            align-items: center;
            gap: 20px;
        }

        .balance {
            background: linear-gradient(135deg, var(--dark-tertiary), var(--dark-secondary));
            padding: 10px 24px;
            border-radius: 40px;
            border: 1px solid var(--border);
            font-weight: 600;
        }

        .balance span {
            color: var(--success);
            font-size: 1.2rem;
            margin-left: 8px;
        }

        .avatar {
            width: 48px;
            height: 48px;
            background: linear-gradient(135deg, var(--primary), var(--primary-dark));
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            font-size: 1.2rem;
            cursor: pointer;
            border: 2px solid rgba(255,255,255,0.1);
            position: relative;
        }

        .avatar::after {
            content: '';
            position: absolute;
            bottom: 2px;
            right: 2px;
            width: 12px;
            height: 12px;
            background: var(--security-high);
            border: 2px solid var(--dark-secondary);
            border-radius: 50%;
        }

        /* OTC контроль баннер */
        .otc-banner {
            background: linear-gradient(135deg, var(--dark-tertiary), var(--dark-secondary));
            border: 1px solid var(--border);
            border-radius: 16px;
            padding: 16px 24px;
            margin: 24px 32px 0;
            display: flex;
            align-items: center;
            justify-content: space-between;
            flex-wrap: wrap;
            gap: 20px;
        }

        .otc-info {
            display: flex;
            align-items: center;
            gap: 32px;
            flex-wrap: wrap;
        }

        .otc-item {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .otc-label {
            color: var(--gray);
            font-size: 0.9rem;
        }

        .otc-value {
            font-size: 1.2rem;
            font-weight: 700;
        }

        .otc-value.win {
            color: var(--success);
        }

        .otc-value.loss {
            color: var(--danger);
        }

        .otc-badge {
            background: rgba(255, 23, 68, 0.1);
            color: var(--danger);
            padding: 8px 16px;
            border-radius: 40px;
            font-size: 0.9rem;
            font-weight: 600;
            border: 1px solid rgba(255, 23, 68, 0.3);
        }

        /* Основной контейнер */
        .app-container {
            display: grid;
            grid-template-columns: 280px 1fr 360px;
            gap: 20px;
            padding: 24px 32px;
            min-height: calc(100vh - 88px);
        }

        /* Левая панель - Активы */
        .assets-panel {
            background: var(--dark-secondary);
            border-radius: 24px;
            padding: 24px;
            border: 1px solid var(--border);
            height: fit-content;
        }

        .panel-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 24px;
        }

        .panel-header h3 {
            font-size: 1.2rem;
            font-weight: 600;
        }

        .panel-header i {
            color: var(--gray);
            cursor: pointer;
            transition: color 0.3s;
        }

        .panel-header i:hover {
            color: var(--light);
        }

        .search-box {
            background: var(--dark-tertiary);
            border-radius: 12px;
            padding: 12px 16px;
            margin-bottom: 24px;
            display: flex;
            align-items: center;
            gap: 12px;
            border: 1px solid var(--border);
        }

        .search-box i {
            color: var(--gray);
        }

        .search-box input {
            background: none;
            border: none;
            color: var(--light);
            width: 100%;
            outline: none;
        }

        .search-box input::placeholder {
            color: var(--gray);
        }

        .asset-category {
            margin-bottom: 24px;
        }

        .category-title {
            color: var(--gray);
            font-size: 0.9rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 16px;
        }

        .asset-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 16px;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.3s;
            margin-bottom: 4px;
            border: 1px solid transparent;
        }

        .asset-item:hover {
            background: var(--dark-tertiary);
            border-color: var(--border);
        }

        .asset-item.active {
            background: var(--dark-tertiary);
            border-color: var(--primary);
        }

        .asset-info {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .asset-icon {
            width: 32px;
            height: 32px;
            background: var(--dark-tertiary);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1rem;
        }

        .asset-name {
            font-weight: 500;
        }

        .asset-price {
            font-weight: 600;
        }

        .asset-change {
            font-size: 0.9rem;
            padding: 4px 8px;
            border-radius: 20px;
        }

        .asset-change.positive {
            background: rgba(0, 200, 83, 0.1);
            color: var(--success);
        }

        .asset-change.negative {
            background: rgba(255, 23, 68, 0.1);
            color: var(--danger);
        }

        /* Центральная панель - График */
        .chart-panel {
            background: var(--dark-secondary);
            border-radius: 24px;
            padding: 24px;
            border: 1px solid var(--border);
        }

        .chart-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 24px;
            flex-wrap: wrap;
            gap: 16px;
        }

        .asset-selector {
            display: flex;
            gap: 8px;
            background: var(--dark-tertiary);
            padding: 4px;
            border-radius: 12px;
        }

        .asset-selector-btn {
            padding: 8px 16px;
            background: none;
            border: none;
            color: var(--gray);
            cursor: pointer;
            border-radius: 8px;
            transition: all 0.3s;
            font-weight: 500;
        }

        .asset-selector-btn:hover {
            color: var(--light);
        }

        .asset-selector-btn.active {
            background: var(--primary);
            color: white;
        }

        .timeframe-selector {
            display: flex;
            gap: 8px;
            background: var(--dark-tertiary);
            padding: 4px;
            border-radius: 12px;
        }

        .timeframe-btn {
            padding: 8px 16px;
            background: none;
            border: none;
            color: var(--gray);
            cursor: pointer;
            border-radius: 8px;
            transition: all 0.3s;
            font-weight: 500;
        }

        .timeframe-btn:hover {
            color: var(--light);
        }

        .timeframe-btn.active {
            background: var(--primary);
            color: white;
        }

        .chart-container {
            height: 500px;
            position: relative;
            margin-bottom: 24px;
        }

        .indicators {
            display: flex;
            gap: 16px;
            flex-wrap: wrap;
        }

        .indicator {
            background: var(--dark-tertiary);
            padding: 12px 20px;
            border-radius: 40px;
            display: flex;
            align-items: center;
            gap: 8px;
            border: 1px solid var(--border);
        }

        .indicator i {
            color: var(--primary);
        }

        .indicator span {
            color: var(--gray);
        }

        .indicator strong {
            color: var(--light);
            margin-left: 4px;
        }

        /* Правая панель - Торговля */
        .trading-panel {
            background: var(--dark-secondary);
            border-radius: 24px;
            padding: 24px;
            border: 1px solid var(--border);
        }

        .current-asset-info {
            background: linear-gradient(135deg, var(--dark-tertiary), var(--dark-secondary));
            border-radius: 20px;
            padding: 24px;
            margin-bottom: 24px;
            border: 1px solid var(--border);
        }

        .current-asset-name {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 16px;
        }

        .current-asset-name h2 {
            font-size: 1.8rem;
            font-weight: 600;
        }

        .current-asset-price {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #fff, var(--primary-light));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .current-asset-change {
            font-size: 1.1rem;
            padding: 8px 16px;
            border-radius: 40px;
            display: inline-block;
        }

        /* Торговые настройки */
        .trade-settings {
            margin-bottom: 24px;
        }

        .setting-group {
            margin-bottom: 24px;
        }

        .setting-label {
            display: flex;
            justify-content: space-between;
            margin-bottom: 12px;
            color: var(--gray);
        }

        .amount-slider {
            width: 100%;
            height: 6px;
            background: var(--dark-tertiary);
            border-radius: 3px;
            outline: none;
            -webkit-appearance: none;
        }

        .amount-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 24px;
            height: 24px;
            background: var(--primary);
            border-radius: 50%;
            cursor: pointer;
            border: 2px solid white;
            box-shadow: 0 4px 12px rgba(41, 98, 255, 0.4);
        }

        .amount-presets {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 8px;
            margin-top: 12px;
        }

        .preset-btn {
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            color: var(--light);
            padding: 8px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            font-weight: 500;
        }

        .preset-btn:hover {
            background: var(--primary);
            border-color: var(--primary);
        }

        .expiry-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 8px;
        }

        .expiry-btn {
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            color: var(--light);
            padding: 12px;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.3s;
            font-weight: 500;
        }

        .expiry-btn:hover {
            background: var(--primary);
            border-color: var(--primary);
        }

        .expiry-btn.active {
            background: var(--primary);
            border-color: var(--primary);
        }

        /* Торговые кнопки */
        .trade-buttons {
            display: flex;
            gap: 16px;
            margin-bottom: 24px;
        }

        .trade-btn {
            flex: 1;
            padding: 20px;
            border: none;
            border-radius: 20px;
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
            position: relative;
            overflow: hidden;
        }

        .btn-up {
            background: linear-gradient(135deg, var(--success), #00a86b);
            color: white;
            box-shadow: 0 8px 24px rgba(0, 200, 83, 0.3);
        }

        .btn-down {
            background: linear-gradient(135deg, var(--danger), #c51162);
            color: white;
            box-shadow: 0 8px 24px rgba(255, 23, 68, 0.3);
        }

        .trade-btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
            transition: left 0.5s;
        }

        .trade-btn:hover::before {
            left: 100%;
        }

        .trade-btn:hover {
            transform: translateY(-4px);
        }

        .trade-btn:active {
            transform: translateY(0);
        }

        .profit-info {
            font-size: 1rem;
            opacity: 0.9;
        }

        /* История сделок */
        .history-panel {
            background: var(--dark-tertiary);
            border-radius: 20px;
            padding: 20px;
            border: 1px solid var(--border);
        }

        .history-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 16px;
        }

        .history-header h4 {
            font-size: 1rem;
            color: var(--gray);
        }

        .history-list {
            max-height: 300px;
            overflow-y: auto;
        }

        .history-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 12px 0;
            border-bottom: 1px solid var(--border);
        }

        .history-item:last-child {
            border-bottom: none;
        }

        .history-direction {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 1.2rem;
        }

        .direction-up {
            background: rgba(0, 200, 83, 0.1);
            color: var(--success);
        }

        .direction-down {
            background: rgba(255, 23, 68, 0.1);
            color: var(--danger);
        }

        .history-details {
            flex: 1;
            padding: 0 12px;
        }

        .history-asset {
            font-weight: 500;
        }

        .history-time {
            font-size: 0.8rem;
            color: var(--gray);
        }

        .history-amount {
            font-weight: 600;
        }

        .history-profit {
            font-weight: 600;
        }

        .profit-positive {
            color: var(--success);
        }

        .profit-negative {
            color: var(--danger);
        }

        /* Модальные окна с безопасностью */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(8px);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: var(--dark-secondary);
            border-radius: 32px;
            width: 90%;
            max-width: 500px;
            border: 1px solid var(--border);
            overflow: hidden;
            animation: modalSecurity 0.3s;
        }

        @keyframes modalSecurity {
            0% {
                opacity: 0;
                transform: scale(0.9);
                border-color: var(--security-high);
            }
            100% {
                opacity: 1;
                transform: scale(1);
            }
        }

        .modal-header {
            padding: 24px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h2 {
            font-size: 1.5rem;
        }

        .modal-header i {
            color: var(--gray);
            cursor: pointer;
            transition: color 0.3s;
            font-size: 1.5rem;
        }

        .modal-header i:hover {
            color: var(--light);
        }

        .modal-body {
            padding: 24px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            color: var(--gray);
        }

        .form-group input, .form-group select {
            width: 100%;
            padding: 16px;
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            border-radius: 16px;
            color: var(--light);
            outline: none;
            font-size: 1rem;
        }

        .form-group input:focus, .form-group select:focus {
            border-color: var(--primary);
        }

        .modal-buttons {
            display: flex;
            gap: 16px;
            margin-top: 24px;
        }

        .btn-primary {
            flex: 1;
            padding: 16px;
            background: linear-gradient(135deg, var(--primary), var(--primary-dark));
            border: none;
            border-radius: 16px;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 24px rgba(41, 98, 255, 0.3);
        }

        .btn-secondary {
            flex: 1;
            padding: 16px;
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            border-radius: 16px;
            color: var(--light);
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-secondary:hover {
            background: var(--dark);
        }

        /* Платежные методы */
        .payment-methods {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
            margin-bottom: 20px;
        }

        .payment-method {
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            border-radius: 16px;
            padding: 16px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .payment-method:hover {
            border-color: var(--primary);
            transform: translateY(-2px);
        }

        .payment-method.active {
            border-color: var(--primary);
            background: rgba(41, 98, 255, 0.1);
        }

        .payment-method i {
            font-size: 2rem;
            color: var(--primary);
        }

        .payment-method span {
            font-weight: 500;
        }

        .payment-method small {
            color: var(--gray);
            font-size: 0.8rem;
        }

        /* Security Dashboard */
        .security-dashboard {
            position: fixed;
            bottom: 20px;
            left: 20px;
            background: var(--dark-secondary);
            border: 1px solid var(--border);
            border-radius: 40px;
            padding: 12px 24px;
            display: flex;
            align-items: center;
            gap: 20px;
            z-index: 90;
            backdrop-filter: blur(10px);
        }

        .security-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.9rem;
        }

        .security-item i {
            color: var(--security-high);
        }

        .security-item .fa-shield-halved {
            color: var(--primary);
        }

        .security-item .fa-lock {
            color: var(--encryption);
        }

        /* Лицензия */
        .license-badge {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: var(--dark-secondary);
            padding: 12px 24px;
            border-radius: 40px;
            border: 1px solid var(--border);
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 0.9rem;
            z-index: 90;
            backdrop-filter: blur(10px);
        }

        .license-badge i {
            color: var(--success);
        }

        /* 2FA модальное окно */
        .twofa-container {
            display: flex;
            gap: 12px;
            justify-content: center;
            margin: 20px 0;
        }

        .twofa-digit {
            width: 50px;
            height: 50px;
            background: var(--dark-tertiary);
            border: 1px solid var(--border);
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            font-weight: 600;
            color: var(--light);
        }

        /* Security tips */
        .security-tip {
            background: rgba(41, 98, 255, 0.1);
            border: 1px solid var(--primary);
            border-radius: 12px;
            padding: 12px;
            margin-top: 20px;
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 0.9rem;
            color: var(--gray-light);
        }

        .security-tip i {
            color: var(--primary);
            font-size: 1.2rem;
        }

        /* Адаптивность */
        @media (max-width: 1400px) {
            .app-container {
                grid-template-columns: 240px 1fr 320px;
                padding: 20px;
            }
        }

        @media (max-width: 1200px) {
            .app-container {
                grid-template-columns: 1fr;
                gap: 20px;
            }
            
            .assets-panel, .trading-panel {
                display: none;
            }
        }

        /* Анимации */
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        .pulse {
            animation: pulse 2s infinite;
        }

        .fade-in {
            animation: fadeIn 0.5s;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
    <!-- Прелоадер с проверкой безопасности -->
    <div class="preloader" id="preloader">
        <div class="security-check">
            <i class="fas fa-shield-halved" style="font-size: 3rem; color: var(--security-high);"></i>
            <h3>Проверка безопасности соединения</h3>
            <div class="security-progress">
                <div class="security-progress-bar"></div>
            </div>
            <div class="security-items">
                <div><i class="fas fa-check-circle"></i> SSL/TLS шифрование</div>
                <div><i class="fas fa-check-circle"></i> Анти-DDoS защита</div>
                <div><i class="fas fa-check-circle"></i> 2FA аутентификация</div>
            </div>
        </div>
    </div>

    <!-- Навигация -->
    <nav class="navbar">
        <div class="logo">
            <i class="fas fa-chart-line"></i>
            <span>OptimaTrade OTC</span>
        </div>

        <div class="security-indicator" onclick="openModal('securityModal')">
            <i class="fas fa-shield-halved"></i>
            <div class="security-level">
                <span>Уровень защиты</span>
                <strong>Максимальный</strong>
            </div>
        </div>
        
        <div class="nav-links">
            <a href="#" class="nav-link active"><i class="fas fa-home"></i>Главная</a>
            <a href="#" class="nav-link"><i class="fas fa-chart-bar"></i>Рынки</a>
            <a href="#" class="nav-link"><i class="fas fa-history"></i>История</a>
            <a href="#" class="nav-link"><i class="fas fa-graduation-cap"></i>Обучение</a>
            <a href="#" class="nav-link"><i class="fas fa-headset"></i>Поддержка</a>
        </div>
        
        <div class="user-menu">
            <div class="balance">
                <i class="fas fa-wallet"></i>
                $<span id="balance">10,000.00</span>
            </div>
            <div class="avatar" onclick="openModal('profileModal')">
                <span>AT</span>
            </div>
        </div>
    </nav>

    <!-- OTC контроль баннер -->
    <div class="otc-banner">
        <div class="otc-info">
            <div class="otc-item">
                <i class="fas fa-chart-pie" style="color: var(--primary);"></i>
                <div>
                    <div class="otc-label">OTC контроль 30/70</div>
                    <div class="otc-value"><span class="otc-value win" id="winRateDisplay">30%</span> / <span class="otc-value loss" id="lossRateDisplay">70%</span></div>
                </div>
            </div>
            <div class="otc-item">
                <i class="fas fa-trophy" style="color: var(--success);"></i>
                <div>
                    <div class="otc-label">Выигрышей</div>
                    <div class="otc-value win" id="totalWins">0</div>
                </div>
            </div>
            <div class="otc-item">
                <i class="fas fa-times-circle" style="color: var(--danger);"></i>
                <div>
                    <div class="otc-label">Проигрышей</div>
                    <div class="otc-value loss" id="totalLosses">0</div>
                </div>
            </div>
            <div class="otc-item">
                <i class="fas fa-chart-line" style="color: var(--warning);"></i>
                <div>
                    <div class="otc-label">Всего сделок</div>
                    <div class="otc-value" id="totalTrades">0</div>
                </div>
            </div>
        </div>
        <div class="otc-badge">
            <i class="fas fa-shield-alt"></i> OTC Protection Active
        </div>
    </div>

    <!-- Security Dashboard -->
    <div class="security-dashboard">
        <div class="security-item">
            <i class="fas fa-shield-halved"></i>
            <span>Firewall: Active</span>
        </div>
        <div class="security-item">
            <i class="fas fa-lock"></i>
            <span>SSL: AES-256</span>
        </div>
        <div class="security-item">
            <i class="fas fa-robot"></i>
            <span>Anti-DDoS: Cloudflare</span>
        </div>
    </div>

    <!-- Лицензия -->
    <div class="license-badge">
        <i class="fas fa-check-circle"></i>
        <span>Лицензия FSC №123456 | Регулируется CySEC</span>
    </div>

    <!-- Основной контейнер -->
    <div class="app-container">
        <!-- Левая панель - Активы (продолжение) -->
        <div class="assets-panel">
            <div class="panel-header">
                <h3>Активы</h3>
                <i class="fas fa-sync-alt" onclick="updateAssets()"></i>
            </div>
            
            <div class="search-box">
                <i class="fas fa-search"></i>
                <input type="text" placeholder="Поиск активов..." id="assetSearch" onkeyup="searchAssets()">
            </div>
            
            <!-- Валюты -->
            <div class="asset-category">
                <div class="category-title">Валюты</div>
                <div class="asset-item active" onclick="selectAsset('EUR/USD', 'forex')">
                    <div class="asset-info">
                        <div class="asset-icon">€</div>
                        <span class="asset-name">EUR/USD</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-EURUSD">1.1050</div>
                        <div class="asset-change positive" id="change-EURUSD">+0.24%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('GBP/USD', 'forex')">
                    <div class="asset-info">
                        <div class="asset-icon">£</div>
                        <span class="asset-name">GBP/USD</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-GBPUSD">1.2650</div>
                        <div class="asset-change negative" id="change-GBPUSD">-0.15%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('USD/JPY', 'forex')">
                    <div class="asset-info">
                        <div class="asset-icon">¥</div>
                        <span class="asset-name">USD/JPY</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-USDJPY">148.50</div>
                        <div class="asset-change positive" id="change-USDJPY">+0.08%</div>
                    </div>
                </div>
            </div>
            
            <!-- Криптовалюты (продолжение) -->
            <div class="asset-category">
                <div class="category-title">Криптовалюты</div>
                <div class="asset-item" onclick="selectAsset('BTC/USD', 'crypto')">
                    <div class="asset-info">
                        <div class="asset-icon">₿</div>
                        <span class="asset-name">BTC/USD</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-BTCUSD">51,200.50</div>
                        <div class="asset-change positive" id="change-BTCUSD">+2.50%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('ETH/USD', 'crypto')">
                    <div class="asset-info">
                        <div class="asset-icon">Ξ</div>
                        <span class="asset-name">ETH/USD</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-ETHUSD">2,850.75</div>
                        <div class="asset-change negative" id="change-ETHUSD">-1.20%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('BNB/USD', 'crypto')">
                    <div class="asset-info">
                        <div class="asset-icon">BNB</div>
                        <span class="asset-name">BNB/USD</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-BNBUSD">350.25</div>
                        <div class="asset-change positive" id="change-BNBUSD">+0.80%</div>
                    </div>
                </div>
            </div>
            
            <!-- Товары -->
            <div class="asset-category">
                <div class="category-title">Товары</div>
                <div class="asset-item" onclick="selectAsset('XAU/USD', 'commodity')">
                    <div class="asset-info">
                        <div class="asset-icon">👑</div>
                        <span class="asset-name">Золото (XAU/USD)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-XAUUSD">2,025.30</div>
                        <div class="asset-change positive" id="change-XAUUSD">+0.30%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('XAG/USD', 'commodity')">
                    <div class="asset-info">
                        <div class="asset-icon">🥈</div>
                        <span class="asset-name">Серебро (XAG/USD)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-XAGUSD">24.50</div>
                        <div class="asset-change negative" id="change-XAGUSD">-0.10%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('OIL/USD', 'commodity')">
                    <div class="asset-info">
                        <div class="asset-icon">🛢️</div>
                        <span class="asset-name">Нефть (OIL/USD)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-OILUSD">78.50</div>
                        <div class="asset-change positive" id="change-OILUSD">+0.50%</div>
                    </div>
                </div>
            </div>
            
            <!-- Акции -->
            <div class="asset-category">
                <div class="category-title">Акции</div>
                <div class="asset-item" onclick="selectAsset('AAPL', 'stock')">
                    <div class="asset-info">
                        <div class="asset-icon">🍎</div>
                        <span class="asset-name">Apple (AAPL)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-AAPL">175.50</div>
                        <div class="asset-change positive" id="change-AAPL">+0.40%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('MSFT', 'stock')">
                    <div class="asset-info">
                        <div class="asset-icon">💻</div>
                        <span class="asset-name">Microsoft (MSFT)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-MSFT">415.30</div>
                        <div class="asset-change positive" id="change-MSFT">+0.25%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('TSLA', 'stock')">
                    <div class="asset-info">
                        <div class="asset-icon">🚗</div>
                        <span class="asset-name">Tesla (TSLA)</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-TSLA">245.30</div>
                        <div class="asset-change negative" id="change-TSLA">-0.80%</div>
                    </div>
                </div>
            </div>
            
            <!-- Индексы -->
            <div class="asset-category">
                <div class="category-title">Индексы</div>
                <div class="asset-item" onclick="selectAsset('SP500', 'index')">
                    <div class="asset-info">
                        <div class="asset-icon">📊</div>
                        <span class="asset-name">S&P 500</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-SP500">5,200.50</div>
                        <div class="asset-change positive" id="change-SP500">+0.15%</div>
                    </div>
                </div>
                <div class="asset-item" onclick="selectAsset('NASDAQ', 'index')">
                    <div class="asset-info">
                        <div class="asset-icon">📈</div>
                        <span class="asset-name">NASDAQ</span>
                    </div>
                    <div>
                        <div class="asset-price" id="price-NASDAQ">18,250.75</div>
                        <div class="asset-change positive" id="change-NASDAQ">+0.20%</div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Центральная панель - График (свечной) -->
        <div class="chart-panel">
            <div class="chart-header">
                <div class="asset-selector">
                    <button class="asset-selector-btn active" onclick="changeAssetGroup('forex')">Forex</button>
                    <button class="asset-selector-btn" onclick="changeAssetGroup('crypto')">Crypto</button>
                    <button class="asset-selector-btn" onclick="changeAssetGroup('commodities')">Товары</button>
                    <button class="asset-selector-btn" onclick="changeAssetGroup('stocks')">Акции</button>
                    <button class="asset-selector-btn" onclick="changeAssetGroup('indices')">Индексы</button>
                </div>
                
                <div class="timeframe-selector">
                    <button class="timeframe-btn active" onclick="changeTimeframe('1m')">1M</button>
                    <button class="timeframe-btn" onclick="changeTimeframe('5m')">5M</button>
                    <button class="timeframe-btn" onclick="changeTimeframe('15m')">15M</button>
                    <button class="timeframe-btn" onclick="changeTimeframe('1h')">1H</button>
                    <button class="timeframe-btn" onclick="changeTimeframe('4h')">4H</button>
                    <button class="timeframe-btn" onclick="changeTimeframe('1d')">1D</button>
                </div>
            </div>
            
            <div class="chart-container">
                <canvas id="priceChart"></canvas>
            </div>
            
            <div class="indicators">
                <div class="indicator">
                    <i class="fas fa-arrow-up"></i>
                    <span>Макс: <strong id="highPrice">1.1080</strong></span>
                </div>
                <div class="indicator">
                    <i class="fas fa-arrow-down"></i>
                    <span>Мин: <strong id="lowPrice">1.1020</strong></span>
                </div>
                <div class="indicator">
                    <i class="fas fa-chart-line"></i>
                    <span>Объем: <strong id="volume">1.2M</strong></span>
                </div>
                <div class="indicator">
                    <i class="fas fa-clock"></i>
                    <span>Обновление: <strong id="updateTime">12:34:56</strong></span>
                </div>
                <div class="indicator">
                    <i class="fas fa-lock" style="color: var(--encryption);"></i>
                    <span>Защита: <strong>AES-256</strong></span>
                </div>
            </div>
        </div>
        
        <!-- Правая панель - Торговля -->
        <div class="trading-panel">
            <div class="current-asset-info">
                <div class="current-asset-name">
                    <i class="fas fa-euro-sign" style="color: var(--primary);"></i>
                    <h2 id="currentAsset">EUR/USD</h2>
                </div>
                <div class="current-asset-price" id="currentPrice">1.1050</div>
                <div class="current-asset-change positive" id="currentChange">+0.24%</div>
            </div>
            
            <div class="trade-settings">
                <div class="setting-group">
                    <div class="setting-label">
                        <span>Сумма сделки</span>
                        <span id="selectedAmount">$100</span>
                    </div>
                    <input type="range" min="1" max="1000" value="100" class="amount-slider" id="amountSlider" oninput="updateAmount()">
                    <div class="amount-presets">
                        <button class="preset-btn" onclick="setAmount(10)">$10</button>
                        <button class="preset-btn" onclick="setAmount(50)">$50</button>
                        <button class="preset-btn" onclick="setAmount(100)">$100</button>
                        <button class="preset-btn" onclick="setAmount(500)">$500</button>
                    </div>
                </div>
                
                <div class="setting-group">
                    <div class="setting-label">
                        <span>Время экспирации</span>
                        <span id="selectedExpiry">1 минута</span>
                    </div>
                    <div class="expiry-grid">
                        <button class="expiry-btn active" onclick="setExpiry(1)">1 мин</button>
                        <button class="expiry-btn" onclick="setExpiry(5)">5 мин</button>
                        <button class="expiry-btn" onclick="setExpiry(15)">15 мин</button>
                        <button class="expiry-btn" onclick="setExpiry(30)">30 мин</button>
                        <button class="expiry-btn" onclick="setExpiry(60)">1 час</button>
                        <button class="expiry-btn" onclick="setExpiry(240)">4 часа</button>
                    </div>
                </div>
                
                <div class="trade-buttons">
                    <button class="trade-btn btn-up" onclick="placeTrade('CALL')">
                        <span>ВВЕРХ</span>
                        <span class="profit-info">Прибыль: $<span id="profitUp">85</span></span>
                    </button>
                    <button class="trade-btn btn-down" onclick="placeTrade('PUT')">
                        <span>ВНИЗ</span>
                        <span class="profit-info">Прибыль: $<span id="profitDown">85</span></span>
                    </button>
                </div>
            </div>
            
            <div class="history-panel">
                <div class="history-header">
                    <h4>История сделок</h4>
                    <i class="fas fa-sync-alt" style="color: var(--gray); cursor: pointer;" onclick="loadHistory()"></i>
                </div>
                <div class="history-list" id="historyList">
                    <!-- История будет загружена динамически -->
                </div>
            </div>
        </div>
    </div>

    <!-- Модальное окно пополнения (криптовалюта + банковская карта) -->
    <div class="modal" id="depositModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Пополнение счета</h2>
                <i class="fas fa-times" onclick="closeModal('depositModal')"></i>
            </div>
            <div class="modal-body">
                <div class="payment-methods">
                    <div class="payment-method active" onclick="selectPaymentMethod('card')">
                        <i class="fas fa-credit-card"></i>
                        <span>Банковская карта</span>
                        <small>Visa, Mastercard</small>
                    </div>
                    <div class="payment-method" onclick="selectPaymentMethod('crypto')">
                        <i class="fas fa-bitcoin"></i>
                        <span>Криптовалюта</span>
                        <small>BTC, ETH, USDT</small>
                    </div>
                </div>
                
                <div class="form-group" id="cardPaymentFields">
                    <label>Номер карты</label>
                    <input type="text" placeholder="**** **** **** ****" maxlength="19" oninput="formatCardNumber(this)">
                </div>
                <div class="form-group" id="cardExpiryFields" style="display: flex; gap: 10px;">
                    <div style="flex: 1;">
                        <label>Срок действия</label>
                        <input type="text" placeholder="MM/YY" maxlength="5" oninput="formatExpiry(this)">
                    </div>
                    <div style="flex: 1;">
                        <label>CVV</label>
                        <input type="password" placeholder="***" maxlength="3">
                    </div>
                </div>
                
                <div class="form-group" id="cryptoPaymentFields" style="display: none;">
                    <label>Выберите криптовалюту</label>
                    <select id="cryptoCurrency">
                        <option>Bitcoin (BTC)</option>
                        <option>Ethereum (ETH)</option>
                        <option>Tether (USDT - TRC20)</option>
                        <option>Binance Coin (BNB)</option>
                    </select>
                </div>
                
                <div class="form-group">
                    <label>Сумма пополнения (USD)</label>
                    <input type="number" id="depositAmount" min="10" max="100000" value="100">
                </div>
                
                <div class="security-tip">
                    <i class="fas fa-lock"></i>
                    <span>Все платежи защищены 256-битным шифрованием. Ваши данные не передаются третьим лицам.</span>
                </div>
                
                <div class="modal-buttons">
                    <button class="btn-secondary" onclick="closeModal('depositModal')">Отмена</button>
                    <button class="btn-primary" onclick="processDeposit()">Пополнить</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Модальное окно вывода -->
    <div class="modal" id="withdrawModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Вывод средств</h2>
                <i class="fas fa-times" onclick="closeModal('withdrawModal')"></i>
            </div>
            <div class="modal-body">
                <div class="payment-methods">
                    <div class="payment-method active" onclick="selectWithdrawMethod('card')">
                        <i class="fas fa-credit-card"></i>
                        <span>Банковская карта</span>
                    </div>
                    <div class="payment-method" onclick="selectWithdrawMethod('crypto')">
                        <i class="fas fa-bitcoin"></i>
                        <span>Криптовалюта</span>
                    </div>
                </div>
                
                <div class="form-group" id="withdrawCardFields">
                    <label>Номер карты получателя</label>
                    <input type="text" placeholder="**** **** **** ****">
                </div>
                
                <div class="form-group" id="withdrawCryptoFields" style="display: none;">
                    <label>Адрес кошелька</label>
                    <input type="text" placeholder="Введите адрес криптокошелька">
                </div>
                
                <div class="form-group">
                    <label>Сумма вывода (USD)</label>
                    <input type="number" id="withdrawAmount" min="10" max="100000" value="100">
                </div>
                
                <div class="form-group">
                    <label>Подтверждение 2FA</label>
                    <div class="twofa-container">
                        <div class="twofa-digit">•</div>
                        <div class="twofa-digit">•</div>
                        <div class="twofa-digit">•</div>
                        <div class="twofa-digit">•</div>
                        <div class="twofa-digit">•</div>
                        <div class="twofa-digit">•</div>
                    </div>
                </div>
                
                <div class="security-tip">
                    <i class="fas fa-shield-alt"></i>
                    <span>Для безопасности вывода требуется подтверждение 2FA и email подтверждение.</span>
                </div>
                
                <div class="modal-buttons">
                    <button class="btn-secondary" onclick="closeModal('withdrawModal')">Отмена</button>
                    <button class="btn-primary" onclick="processWithdraw()">Вывести</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Модальное окно безопасности -->
    <div class="modal" id="securityModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Безопасность платформы</h2>
                <i class="fas fa-times" onclick="closeModal('securityModal')"></i>
            </div>
            <div class="modal-body">
                <div style="display: flex; flex-direction: column; gap: 20px;">
                    <div style="display: flex; align-items: center; gap: 20px; background: var(--dark-tertiary); padding: 16px; border-radius: 16px;">
                        <i class="fas fa-shield-halved" style="font-size: 2rem; color: var(--security-high);"></i>
                        <div>
                            <h3>Статус защиты: Максимальный</h3>
                            <p style="color: var(--gray);">Все системы работают в штатном режиме</p>
                        </div>
                    </div>
                    
                    <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px;">
                        <div style="background: var(--dark-tertiary); padding: 16px; border-radius: 12px;">
                            <i class="fas fa-lock" style="color: var(--encryption);"></i>
                            <h4 style="margin: 10px 0;">Шифрование</h4>
                            <p style="color: var(--gray); font-size: 0.9rem;">AES-256, SSL/TLS 1.3</p>
                        </div>
                        <div style="background: var(--dark-tertiary); padding: 16px; border-radius: 12px;">
                            <i class="fas fa-robot" style="color: var(--primary);"></i>
                            <h4 style="margin: 10px 0;">Anti-DDoS</h4>
                            <p style="color: var(--gray); font-size: 0.9rem;">Cloudflare Enterprise</p>
                        </div>
                        <div style="background: var(--dark-tertiary); padding: 16px; border-radius: 12px;">
                            <i class="fas fa-fingerprint" style="color: var(--warning);"></i>
                            <h4 style="margin: 10px 0;">2FA</h4>
                            <p style="color: var(--gray); font-size: 0.9rem;">Google Authenticator</p>
                        </div>
                        <div style="background: var(--dark-tertiary); padding: 16px; border-radius: 12px;">
                            <i class="fas fa-database" style="color: var(--success);"></i>
                            <h4 style="margin: 10px 0;">Резервное копирование</h4>
                            <p style="color: var(--gray); font-size: 0.9rem;">Каждые 15 минут</p>
                        </div>
                    </div>
                    
                    <div style="background: rgba(0, 200, 83, 0.1); padding: 16px; border-radius: 12px; border: 1px solid var(--success);">
                        <p style="color: var(--success);"><i class="fas fa-check-circle"></i> Антифрод система активна</p>
                        <p style="color: var(--gray); font-size: 0.9rem; margin-top: 8px;">Заблокировано подозрительных попыток входа: 1,247</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Модальное окно профиля -->
    <div class="modal" id="profileModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Профиль пользователя</h2>
                <i class="fas fa-times" onclick="closeModal('profileModal')"></i>
            </div>
            <div class="modal-body">
                <div class="form-group">
                    <label>ID пользователя</label>
                    <input type="text" value="AT2024-123456" readonly>
                </div>
                <div class="form-group">
                    <label>Имя</label>
                    <input type="text" value="Alex Trader">
                </div>
                <div class="form-group">
                    <label>Email</label>
                    <input type="email" value="alex@example.com">
                </div>
                <div class="form-group">
                    <label>Телефон</label>
                    <input type="tel" value="+1234567890">
                </div>
                <div class="form-group">
                    <label>2FA аутентификация</label>
                    <div style="display: flex; gap: 10px;">
                        <input type="text" value="Активно" readonly style="color: var(--success);">
                        <button class="btn-primary" style="flex: 0; padding: 12px 20px;">Настроить</button>
                    </div>
                </div>
                <div class="modal-buttons">
                    <button class="btn-primary" onclick="closeModal('profileModal')">Сохранить</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let currentAsset = 'EUR/USD';
        let currentAssetType = 'forex';
        let currentTimeframe = '1m';
        let currentAmount = 100;
        let currentExpiry = 1;
        let userBalance = 10000;
        let chart;
        let tradeHistory = [];
        let priceUpdateInterval;
        
        // OTC контроль 30/70
        let totalTrades = 0;
        let totalWins = 0;
        let totalLosses = 0;
        const targetWinRate = 30; // 30% выигрышей
        const targetLossRate = 70; // 70% проигрышей

        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', function() {
            // Убираем прелоадер через 2 секунды
            setTimeout(() => {
                document.getElementById('preloader').classList.add('fade-out');
            }, 2000);

            // Инициализируем свечной график
            initCandlestickChart();
            
            // Загружаем историю
            loadHistory();
            
            // Запускаем обновление цен
            startPriceUpdates();
            
            // Обновляем время
            updateTime();
            setInterval(updateTime, 1000);
        });

        // Обновление времени
        function updateTime() {
            const now = new Date();
            document.getElementById('updateTime').textContent = 
                now.toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
        }

        // Инициализация свечного графика
        function initCandlestickChart() {
            const ctx = document.getElementById('priceChart').getContext('2d');
            
            // Генерируем свечные данные
            const labels = [];
            const candlestickData = [];
            let price = 1.1050;
            
            for (let i = 0; i < 50; i++) {
                labels.push('');
                
                const open = price;
                const close = price + (Math.random() - 0.5) * 0.002;
                const high = Math.max(open, close) + Math.random() * 0.001;
                const low = Math.min(open, close) - Math.random() * 0.001;
                
                candlestickData.push({
                    x: i,
                    o: open,
                    h: high,
                    l: low,
                    c: close
                });
                
                price = close;
            }
            
            // Для свечного графика используем candlestick плагин или рисуем как линию (упрощенно)
            chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: currentAsset,
                        data: candlestickData.map(d => d.c),
                        borderColor: '#2962ff',
                        backgroundColor: 'rgba(41, 98, 255, 0.1)',
                        borderWidth: 3,
                        fill: true,
                        tension: 0.4,
                        pointRadius: 0,
                        pointHoverRadius: 6
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            mode: 'index',
                            intersect: false,
                            backgroundColor: '#1e2738',
                            titleColor: '#fff',
                            bodyColor: '#b9c0d1',
                            borderColor: 'rgba(255,255,255,0.08)',
                            borderWidth: 1,
                            callbacks: {
                                label: function(context) {
                                    const data = candlestickData[context.dataIndex];
                                    return [
                                        `O: $${data.o.toFixed(4)}`,
                                        `H: $${data.h.toFixed(4)}`,
                                        `L: $${data.l.toFixed(4)}`,
                                        `C: $${data.c.toFixed(4)}`
                                    ];
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: { display: false },
                            ticks: { display: false }
                        },
                        y: {
                            grid: { color: 'rgba(255,255,255,0.05)' },
                            ticks: { color: '#8a93a7' }
                        }
                    }
                }
            });
        }

        // Обновление цен с OTC контролем 30/70
        function startPriceUpdates() {
            priceUpdateInterval = setInterval(() => {
                // Обновляем цены для всех активов
                updateAllPrices();
                
                // Обновляем OTC статистику
                updateOTCStats();
                
                // Добавляем свечу на график
                if (chart) {
                    const lastPrice = chart.data.datasets[0].data[chart.data.datasets[0].data.length - 1];
                    const change = (Math.random() - 0.5) * 0.001;
                    const newPrice = lastPrice * (1 + change);
                    
                    chart.data.datasets[0].data.push(newPrice);
                    chart.data.datasets[0].data.shift();
                    chart.update();
                }
                
            }, 3000);
        }

        // Обновление всех цен
        function updateAllPrices() {
            const assets = {
                'EURUSD': { element: 'EURUSD', price: 1.1050 },
                'GBPUSD': { element: 'GBPUSD', price: 1.2650 },
                'USDJPY': { element: 'USDJPY', price: 148.50 },
                'AUDUSD': { element: 'AUDUSD', price: 0.6750 },
                'USDCAD': { element: 'USDCAD', price: 1.3450 },
                'NZDUSD': { element: 'NZDUSD', price: 0.6150 },
                'USDCHF': { element: 'USDCHF', price: 0.8850 },
                'BTCUSD': { element: 'BTCUSD', price: 51200.50 },
                'ETHUSD': { element: 'ETHUSD', price: 2850.75 },
                'BNBUSD': { element: 'BNBUSD', price: 350.25 },
                'SOLUSD': { element: 'SOLUSD', price: 105.80 },
                'XRPUSD': { element: 'XRPUSD', price: 0.55 },
                'ADAUSD': { element: 'ADAUSD', price: 0.38 },
                'DOGEUSD': { element: 'DOGEUSD', price: 0.085 },
                'XAUUSD': { element: 'XAUUSD', price: 2025.30 },
                'XAGUSD': { element: 'XAGUSD', price: 24.50 },
                'OILUSD': { element: 'OILUSD', price: 78.50 },
                'AAPL': { element: 'AAPL', price: 175.50 },
                'MSFT': { element: 'MSFT', price: 415.30 },
                'TSLA': { element: 'TSLA', price: 245.30 },
                'SP500': { element: 'SP500', price: 5200.50 },
                'NASDAQ': { element: 'NASDAQ', price: 18250.75 }
            };
            
            for (let [key, data] of Object.entries(assets)) {
                const priceElement = document.getElementById(`price-${data.element}`);
                const changeElement = document.getElementById(`change-${data.element}`);
                
                if (priceElement) {
                    const change = (Math.random() - 0.5) * 0.002;
                    const newPrice = data.price * (1 + change);
                    
                    let formattedPrice;
                    if (key.includes('BTC') || key.includes('ETH') || key.includes('SP500') || key.includes('NASDAQ')) {
                        formattedPrice = newPrice.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",");
                    } else if (key.includes('JPY')) {
                        formattedPrice = newPrice.toFixed(3);
                    } else if (key.includes('XRP') || key.includes('ADA') || key.includes('DOGE')) {
                        formattedPrice = newPrice.toFixed(4);
                    } else {
                        formattedPrice = newPrice.toFixed(4);
                    }
                    
                    priceElement.textContent = formattedPrice;
                    
                    const percentChange = ((newPrice - data.price) / data.price * 100);
                    changeElement.textContent = (percentChange > 0 ? '+' : '') + percentChange.toFixed(2) + '%';
                    changeElement.className = percentChange >= 0 ? 'asset-change positive' : 'asset-change negative';
                    
                    data.price = newPrice;
                }
            }
            
            // Обновляем текущий выбранный актив
            updateCurrentAssetPrice();
        }

        // Обновление текущей цены
        function updateCurrentAssetPrice() {
            const priceElement = document.getElementById('currentPrice');
            const changeElement = document.getElementById('currentChange');
            
            if (currentAsset === 'EUR/USD') {
                const price = document.getElementById('price-EURUSD').textContent;
                const change = document.getElementById('change-EURUSD').textContent;
                priceElement.textContent = price;
                changeElement.textContent = change;
                changeElement.className = change.includes('+') ? 'current-asset-change positive' : 'current-asset-change negative';
            } else if (currentAsset === 'GBP/USD') {
                const price = document.getElementById('price-GBPUSD').textContent;
                const change = document.getElementById('change-GBPUSD').textContent;
                priceElement.textContent = price;
                changeElement.textContent = change;
                changeElement.className = change.includes('+') ? 'current-asset-change positive' : 'current-asset-change negative';
            }
        }

        // Обновление OTC статистики
        function updateOTCStats() {
            totalTrades = tradeHistory.length;
            totalWins = tradeHistory.filter(t => t.profit > 0).length;
            totalLosses = tradeHistory.filter(t => t.profit < 0).length;
            
            const winRate = totalTrades > 0 ? (totalWins / totalTrades * 100).toFixed(1) : 30;
            const lossRate = totalTrades > 0 ? (totalLosses / totalTrades * 100).toFixed(1) : 70;
            
            document.getElementById('winRateDisplay').textContent = winRate + '%';
            document.getElementById('lossRateDisplay').textContent = lossRate + '%';
            document.getElementById('totalWins').textContent = totalWins;
            document.getElementById('totalLosses').textContent = totalLosses;
            document.getElementById('totalTrades').textContent = totalTrades;
        }

        // Выбор актива
        function selectAsset(asset, type) {
            currentAsset = asset;
            currentAssetType = type;
            
            document.querySelectorAll('.asset-item').forEach(item => {
                item.classList.remove('active');
            });
            event.currentTarget.classList.add('active');
            
            document.getElementById('currentAsset').textContent = asset;
            updateCurrentAssetPrice();
        }

        // Смена группы активов
        function changeAssetGroup(group) {
            document.querySelectorAll('.asset-selector-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
        }

        // Смена таймфрейма
        function changeTimeframe(tf) {
            currentTimeframe = tf;
            
            document.querySelectorAll('.timeframe-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
        }

        // Обновление суммы
        function updateAmount() {
            currentAmount = parseInt(document.getElementById('amountSlider').value);
            document.getElementById('selectedAmount').textContent = '$' + currentAmount;
            updateProfit();
        }

        function setAmount(amount) {
            currentAmount = amount;
            document.getElementById('amountSlider').value = amount;
            document.getElementById('selectedAmount').textContent = '$' + amount;
            updateProfit();
        }

        // Установка времени экспирации
        function setExpiry(minutes) {
            currentExpiry = minutes;
            
            document.querySelectorAll('.expiry-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
            
            let displayText = minutes + ' мин';
            if (minutes >= 60) {
                displayText = (minutes / 60) + ' час';
                if (minutes > 60) displayText += 'а';
            }
            document.getElementById('selectedExpiry').textContent = displayText;
            
            updateProfit();
        }

        // Обновление прибыли
        function updateProfit() {
            const profit = Math.floor(currentAmount * 0.85);
            document.getElementById('profitUp').textContent = profit;
            document.getElementById('profitDown').textContent = profit;
        }

        // Размещение сделки с OTC контролем 30/70
        function placeTrade(direction) {
            if (currentAmount > userBalance) {
                alert('Недостаточно средств на балансе!');
                return;
            }
            
            // OTC контроль 30/70
            // Рассчитываем вероятность выигрыша на основе текущей статистики
            let winProbability = 0.3; // Базовая 30%
            
            if (totalTrades > 10) {
                const currentWinRate = totalWins / totalTrades;
                const deviation = currentWinRate - 0.3;
                
                // Корректируем вероятность для достижения 30%
                if (currentWinRate < 0.3) {
                    winProbability = 0.35; // Увеличиваем шанс если выигрышей мало
                } else if (currentWinRate > 0.3) {
                    winProbability = 0.25; // Уменьшаем шанс если выигрышей много
                }
            }
            
            // Определяем результат
            const isWin = Math.random() < winProbability;
            
            // Вычитаем сумму
            userBalance -= currentAmount;
            
            let profit = 0;
            if (isWin) {
                profit = Math.floor(currentAmount * 0.85);
                userBalance += currentAmount + profit;
            }
            
            document.getElementById('balance').textContent = userBalance.toFixed(2);
            
            // Добавляем в историю
            const trade = {
                id: Date.now(),
                asset: currentAsset,
                direction: direction,
                amount: currentAmount,
                profit: isWin ? profit : -currentAmount,
                time: new Date().toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit', second: '2-digit' })
            };
            
            tradeHistory.unshift(trade);
            
            // Обновляем историю и статистику
            loadHistory();
            updateOTCStats();
            
            // Показываем результат
            alert(isWin ? 
                `✅ Поздравляем! Вы выиграли $${profit}!` : 
                `❌ Сделка закрыта с убытком $${currentAmount}`
            );
        }

        // Загрузка истории
        function loadHistory() {
            const historyList = document.getElementById('historyList');
            
            if (tradeHistory.length === 0) {
                // Демо-история с учетом OTC 30/70
                tradeHistory = [
                    { id: 6, asset: 'BTC/USD', direction: 'PUT', amount: 200, profit: -200, time: '12:38:22' },
                    { id: 5, asset: 'GBP/USD', direction: 'PUT', amount: 150, profit: -150, time: '12:34:56' },
                    { id: 4, asset: 'AAPL', direction: 'CALL', amount: 75, profit: -75, time: '12:28:15' },
                    { id: 3, asset: 'EUR/USD', direction: 'CALL', amount: 100, profit: 85, time: '12:15:43' },
                    { id: 2, asset: 'ETH/USD', direction: 'PUT', amount: 50, profit: -50, time: '11:52:08' },
                    { id: 1, asset: 'GOLD', direction: 'CALL', amount: 300, profit: -300, time: '11:34:56' }
                ];
            }
            
            historyList.innerHTML = '';
            tradeHistory.slice(0, 10).forEach(trade => {
                const item = document.createElement('div');
                item.className = 'history-item';
                
                item.innerHTML = `
                    <div class="history-direction ${trade.direction === 'CALL' ? 'direction-up' : 'direction-down'}">
                        ${trade.direction === 'CALL' ? '↑' : '↓'}
                    </div>
                    <div class="history-details">
                        <div class="history-asset">${trade.asset}</div>
                        <div class="history-time">${trade.time}</div>
                    </div>
                    <div class="history-amount">$${trade.amount}</div>
                    <div class="history-profit ${trade.profit > 0 ? 'profit-positive' : 'profit-negative'}">
                        ${trade.profit > 0 ? '+' : ''}$${Math.abs(trade.profit)}
                    </div>
                `;
                
                historyList.appendChild(item);
            });
        }

        // Поиск активов
        function searchAssets() {
            const searchTerm = document.getElementById('assetSearch').value.toLowerCase();
            const assetItems = document.querySelectorAll('.asset-item');
            
            assetItems.forEach(item => {
                const name = item.querySelector('.asset-name').textContent.toLowerCase();
                if (name.includes(searchTerm) || searchTerm === '') {
                    item.style.display = 'flex';
                } else {
                    item.style.display = 'none';
                }
            });
        }

        // Обновление активов
        function updateAssets() {
            alert('Обновление списка активов...');
            updateAllPrices();
        }

        // Форматирование номера карты
        function formatCardNumber(input) {
            let value = input.value.replace(/\D/g, '');
            if (value.length > 16) value = value.slice(0, 16);
            
            let formatted = '';
            for (let i = 0; i < value.length; i++) {
                if (i > 0 && i % 4 === 0) formatted += ' ';
                formatted += value[i];
            }
            input.value = formatted;
        }

        // Форматирование срока действия
        function formatExpiry(input) {
            let value = input.value.replace(/\D/g, '');
            if (value.length > 4) value = value.slice(0, 4);
            
            if (value.length >= 2) {
                value = value.slice(0, 2) + '/' + value.slice(2);
            }
            input.value = value;
        }

        // Выбор метода оплаты
        function selectPaymentMethod(method) {
            document.querySelectorAll('.payment-method').forEach(el => {
                el.classList.remove('active');
            });
            event.currentTarget.classList.add('active');
            
            if (method === 'card') {
                document.getElementById('cardPaymentFields').style.display = 'block';
                document.getElementById('cardExpiryFields').style.display = 'flex';
                document.getElementById('cryptoPaymentFields').style.display = 'none';
            } else {
                document.getElementById('cardPaymentFields').style.display = 'none';
                document.getElementById('cardExpiryFields').style.display = 'none';
                document.getElementById('cryptoPaymentFields').style.display = 'block';
            }
        }

        // Выбор метода вывода
        function selectWithdrawMethod(method) {
            document.querySelectorAll('.payment-method').forEach(el => {
                el.classList.remove('active');
            });
            event.currentTarget.classList.add('active');
            
            if (method === 'card') {
                document.getElementById('withdrawCardFields').style.display = 'block';
                document.getElementById('withdrawCryptoFields').style.display = 'none';
            } else {
                document.getElementById('withdrawCardFields').style.display = 'none';
                document.getElementById('withdrawCryptoFields').style.display = 'block';
            }
        }

        // Модальные окна
        function openModal(modalId) {
            document.getElementById(modalId).classList.add('active');
        }

        function closeModal(modalId) {
            document.getElementById(modalId).classList.remove('active');
        }

        // Обработка пополнения
        function processDeposit() {
            const amount = parseInt(document.getElementById('depositAmount').value);
            userBalance += amount;
            document.getElementById('balance').textContent = userBalance.toFixed(2);
            closeModal('depositModal');
            alert(`✅ Счет пополнен на $${amount}`);
        }

        // Обработка вывода
        function processWithdraw() {
            const amount = parseInt(document.getElementById('withdrawAmount').value);
            
            if (amount > userBalance) {
                alert('❌ Недостаточно средств');
                return;
            }
            
            userBalance -= amount;
            document.getElementById('balance').textContent = userBalance.toFixed(2);
            closeModal('withdrawModal');
            alert(`✅ Запрос на вывод $${amount} отправлен. Проверьте 2FA код на вашем телефоне.`);
        }

        // Очистка интервала при закрытии
        window.addEventListener('beforeunload', function() {
            if (priceUpdateInterval) {
                clearInterval(priceUpdateInterval);
            }
        });
    </script>
</body>
</html>
