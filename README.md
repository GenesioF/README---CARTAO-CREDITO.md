// Elementos DOM
const cardNumberInput = document.getElementById('card-number');
const cardNumberDisplay = document.getElementById('card-number-display');
const cardBrandElement = document.getElementById('card-brand');
const cardLogoImg = document.getElementById('card-logo-img');
const identifyBtn = document.getElementById('identify-btn');
const clearBtn = document.getElementById('clear-btn');
const cardFront = document.querySelector('.card-front');

// Regex para identificar as bandeiras de cartão
const cardPatterns = {
    visa: {
        regex: /^4[0-9]{12}(?:[0-9]{3})?$/,
        logo: 'img/visa.png',
        cssClass: 'visa-bg',
        name: 'Visa'
    },
    mastercard: {
        regex: /^(5[1-5][0-9]{14}|2(22[1-9][0-9]{12}|2[3-9][0-9]{13}|[3-6][0-9]{14}|7[0-1][0-9]{13}|720[0-9]{12}))$/,
        logo: 'img/mastercard.png',
        cssClass: 'mastercard-bg',
        name: 'MasterCard'
    },
    amex: {
        regex: /^3[47][0-9]{13}$/,
        logo: 'img/amex.png',
        cssClass: 'amex-bg',
        name: 'American Express'
    },
    diners: {
        regex: /^3(?:0[0-5]|[68][0-9])[0-9]{11}$/,
        logo: 'img/diners.png',
        cssClass: 'diners-bg',
        name: 'Diners Club'
    },
    discover: {
        regex: /^6(?:011|5[0-9]{2})[0-9]{12}$/,
        logo: 'img/discover.png',
        cssClass: 'discover-bg',
        name: 'Discover'
    },
    jcb: {
        regex: /^(?:2131|1800|35\d{3})\d{11}$/,
        logo: 'img/jcb.png',
        cssClass: 'jcb-bg',
        name: 'JCB'
    }
};

// Adicionar máscara ao input de número do cartão
cardNumberInput.addEventListener('input', function(e) {
    // Remove tudo que não for número
    let cardNumber = e.target.value.replace(/\D/g, '');
    
    // Aplica a formatação com espaços a cada 4 dígitos
    let formattedCardNumber = '';
    for (let i = 0; i < cardNumber.length; i++) {
        if (i > 0 && i % 4 === 0) {
            formattedCardNumber += ' ';
        }
        formattedCardNumber += cardNumber[i];
    }
    
    // Atualiza o valor do input e exibe no cartão
    e.target.value = formattedCardNumber;
    updateCardDisplay(formattedCardNumber);
});

// Atualiza o display do cartão
function updateCardDisplay(number) {
    if (number.length === 0) {
        cardNumberDisplay.textContent = '•••• •••• •••• ••••';
    } else {
        cardNumberDisplay.textContent = number;
    }
}

// Função para identificar a bandeira do cartão
function identifyCardBrand() {
    // Remove espaços do número do cartão
    const cardNumber = cardNumberInput.value.replace(/\s/g, '');
    
    // Limpa estilos anteriores
    resetCardStyles();
    
    // Verifica se o campo está vazio
    if (cardNumber.length === 0) {
        setUnknownCard();
        return;
    }
    
    // Percorre os padrões para identificar a bandeira
    let identifiedBrand = null;
    
    for (const [brand, pattern] of Object.entries(cardPatterns)) {
        if (pattern.regex.test(cardNumber)) {
            identifiedBrand = brand;
            break;
        }
    }
    
    // Exibe o resultado
    if (identifiedBrand) {
        const brandInfo = cardPatterns[identifiedBrand];
        
        // Atualiza o texto da bandeira
        cardBrandElement.textContent = brandInfo.name;
        
        // Adiciona a classe CSS correspondente
        cardFront.classList.add(brandInfo.cssClass);
        
        // Atualiza a imagem da logo
        cardLogoImg.src = brandInfo.logo;
        cardLogoImg.alt = `Logo ${brandInfo.name}`;
        cardLogoImg.style.display = 'block';
    } else {
        setUnknownCard();
    }
}

// Função para definir cartão desconhecido
function setUnknownCard() {
    cardBrandElement.textContent = 'Não identificada';
    cardFront.classList.add('unknown-bg');
    cardLogoImg.style.display = 'none';
}

// Reset do card para o estado inicial
function resetCardStyles() {
    // Remove todas as classes de bandeira
    for (const pattern of Object.values(cardPatterns)) {
        cardFront.classList.remove(pattern.cssClass);
    }
    cardFront.classList.remove('unknown-bg');
    
    // Reset da logo
    cardLogoImg.src = '';
    cardLogoImg.alt = '';
}

// Função para limpar o formulário
function clearForm() {
    cardNumberInput.value = '';
    updateCardDisplay('');
    resetCardStyles();
    cardBrandElement.textContent = 'Não identificada';
    cardFront.classList.add('unknown-bg');
    cardLogoImg.style.display = 'none';
}

// Event listeners
identifyBtn.addEventListener('click', identifyCardBrand);
clearBtn.addEventListener('click', clearForm);

// Inicializa o formulário
clearForm();
