---

title: Configuring a Smart Card Reader in a VirtualBox Windows 10 VM
date: '2019-05-11 3:45:00'
---

Smart cards and smart card readers generally work fine in Mac OS when used with a web browser, but they can be a bit challenging to use to digitally sign a document, such as a PDF. In these cases, I find it easier to use a Windows virtual machine for digital signing. I prefer the open source software [VirtualBox](https://www.virtualbox.org/) to run VMs on my Mac. While outside the scope of this post, plenty of information exists to help with the process of [installing a Windows 10 VM in VirtualBox](https://www.google.com/search?q=install+windows+10+vm+virtualbox&oq=set+up+windows+10+vm).

In an out-of-the-box Windows 10 VM, attempting to digitally sign a document such as a PDF will likely result in a prompt from your PDF reader asking you to manage/configure your certificates, such as the one below from Adobe Reader:

<img src="../assets/img/smart-card/configure-digital-id.png" width="524" height="372" alt="Prompt to configure digital ID">

This prompt means the certificates on the smart card are not being recognized by the system. If they were, a dialog would appear displaying the available certificates. The light on the smart card reader (if it has one) will also be off instead of lighting up as the system reads the card.

The following steps describe how to configure a VirtualBox Windows 10 VM to use a smart card reader:

1. Power off the VM
2. Ensure the smart card reader is plugged in
3. Go to Settings for the VM in VirtualBox
4. In the Settings dialog, go to Ports, then USB
    1. Ensure the "Enable USB Controller" box is checked
    2. Click the USB icon with the plus (+) icon on the right-hand side of the "USB Device Filters" box
	<img src="../assets/img/smart-card/add-card-reader.png" width="752" height="378" alt="Add new USB filter" />
    3. A list of attached devices will appear
    4. Select the smart card reader from the list
    5. The default configuration should be fine
    6. Choose OK, and the card reader will added to the list of USB device filters 
	<img src="../assets/img/smart-card/card-reader-added.png" width="752" height="378" alt="USB device filter added" />
    7. Close Settings
5. Start the VM
6. Go to the Windows Device Manager
    1. Device Manager should have entries for "Smart card readers" and "Smart cards"
    2. The smart card reader should NOT BE listed under "Universal Serial Bus controllers"
    3. Expand the "Smart card readers" node
        1. Right-click on the node
        2. Choose "update driver" from the context menu
        3. Follow the steps in the dialog that appears to update the driver(s)
        4. Once the update is complete, the smart card reader make/model should be listed instead of a generic entry
    4. Repeat the previous steps for the entry under the "Smart cards" node

Re-open the document and digitally sign it. The PDF reader should display the certificates found on the smart card. Choose the appropriate certificate, and the system should prompt you to enter the smart card PIN. You will also be prompted to save the document as a new document.

Close the original document and open the newly-created  document to confirm that it is indeed digitally signed.


