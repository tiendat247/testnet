Below is a guide for you to join the testnet assisterr and the code to automatically claim tokens.

 First, go to the following link to register your wallet to join the testnet
[https://build.assisterr.ai](https://build.assisterr.ai/?ref=669024cc9ca1ab63a8295c3c)

Use Phantom wallet to connect your wallet already

Go ahead, connect your discord + X to get 5 tokens

Complete tasks in the list (simply click)

The code below allows your browser to automatically claim synthetic tokens every 12 hours

    function dailyClaim() {
        var claimButton = document.querySelector("button.inline-flex.items-center.justify-center.whitespace-nowrap.rounded-md.text-sm.font-medium.ring-offset-background.transition-colors.disabled\\:pointer-events-none.disabled\\:opacity-50.focus-visible\\:outline-\\[darkgrey\\].border.border-input.bg-background.hover\\:bg-accent.hover\\:text-accent-foreground.h-10.px-4.py-2.mt-4.md\\:mt-12.max-w-96.w-full");
        
        if (claimButton) {
            claimButton.click();
            console.log("Daily claim yapıldı");
        } else {
            console.log("Daily claim butonu bulunamadı");
        }
    }
    
    setInterval(dailyClaim, 43200000);
    dailyClaim();
![image](https://github.com/ruesandora/Rivalz/assets/101149671/b484022a-82b2-4d92-a2d0-b5be37db345d)
