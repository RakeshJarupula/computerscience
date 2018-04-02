# Build the machine learning model that powers ContosoBNB #

<a name="Overview"></a>

## Overview ##

In this hands-on lab, you are acting as a newly-hired data scientist for a company building a short-term vacation rental platform called ContosoBNB. Your goal is to begin building, training, and tuning a machine learning model to suggest a rental rate that maximizes revenue for rental property owners.

First, you will create a simple dev environment using the [Data Science Virtual Machine (DSVM)](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) in Azure. This VM image is built specifically for data science workloads with platform support for Windows Server, Ubuntu Linux, and CentOS Linux. The DSVM comes preconfigured with many popular open-source tools, including [Jupyter](https://jupyter.org/), [RStudio](https://www.rstudio.com/), and [scikit-learn](https://www.rstudio.com/), which is a free, open-source machine learning toolkit for Python programmers.

After you create a Linux operating system (OS)-based DSVM and connect to it, you will import a dataset and then use the scikit-learn API to create and refine an ML model to use with your dataset.

<a name="Objectives"></a>

### Objectives ###

In this HOL, you will learn how to:

* Create a Linux OS-based DSVM
* Connect to the DSVM using a remote desktop client
* Save a data file from GitHub to the DSVM
* Save a Jupyter Notebook file from GitHub to the DSVM
* Use [pandas](http://pandas.pydata.org/) to filter columns in a dataset
* Use pandas to calculate values in a column
* Use scikit-learn to split the data into separate datasets for training and testing
* Use scikit-learn to create an ML model
* Use scikit-learn to analyze the model's accuracy

<a name="Prerequisites"></a>

### Prerequisites ###

The following are required to complete this HOL:

* An Azure subscription, which will be used to create the VM and query Data Lake. Students can get access through [Azure for Students](http://aka.ms/azure4students).
* [X2Go](https://wiki.x2go.org/doku.php/download:start), an [Xfce](https://xfce.org/) remote-desktop client. [Installation instructions](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).

**Note:** To quickly verify your student status, use your school-issued email address like "your_name@your_school.edu" or equivalent. This will become your Microsoft Account that you can use to login to the [Azure Portal](http://portal.azure.com).

<a name="Resources"></a>

### Resources ###

This lab uses an existing dataset (released under public domain) to model real-world property listings with their associated details. The complete dataset can be found [here](http://insideairbnb.com/get-the-data.html).

<a name="Exercises"></a>

## Exercises ##

This HOL includes the following exercises:

* [Exercise 1: Create a DSVM](#Exercise1)
* [Exercise 2: Connect to the DSVM](#Exercise2)
* [Exercise 3: Download a dataset and prepare a Jupyter notebook](#Exercise3)
* [Exercise 4: Import data, clean data, and make predictions using Python/scikit-learn in a Jupyter notebook](#Exercise4)

<a name="Exercise1"></a>

## Exercise 1: Create a DSVM ##

In this exercise, you will create an instance of the DSVM for Linux in Azure:

### Step 1: Creating the DSVM in Azure ###

1. In a web browser, open the [Azure Portal](https://portal.azure.com/) and then sign-in with your Microsoft Account (use the account your Azure subscription is associated with).

1. From the left-side menu, click the **+** sign to add a new resource.

    ![CreateResource](images/CreateResource.jpg)

1. In the **Search** field, type **data science**. From the list of matching results, click **Data Science Virtual Machine for Linux (Ubuntu)**.

    ![FindDSVM](images/FindDSVM.jpg)

1. Take a few moments to read the description of the DSVM for Linux (Ubuntu), and then click **Create**.

    ![CreateDSVM](images/CreateDSVM.jpg)

1. In the **Name** field, enter a name for your VM; for example, **MyDSVM**.

    ![CreateDSVM2](images/CreateDSVM2.jpg)

1. In the **VM disk type** field, select **SSD**.

1. In the **User name** field, type a user name of your choice. Save this information, because you will use it to sign in to the VM later.

1. For **Authentication type**, select **Password**.

1. In the **Password** field, enter a password of your choice that meets the following requirements:
    * Must be between 12 and 72 characters long
    * Must contain three of the following:
        * One lowercase letter
        * One uppercase letter
        * One number
        * One special character that is not "\" or "-"

   Save your username and password, because you will use it to sign in to the VM later.

1. In the **Subscription** drop-down menu, select your current Azure Subscription. Most users will only have one but it's possible to have many.

1. In the **Resource group** section, leave **Create new** selected, and then enter a name of your choice for the Resource group in the field below; for example, **DataScienceGroup1**.

    A [resource group](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) in Azure is a container for the resources used to run an application. Resource groups help administrators organize monitoring, access control, provisioning, and billing. Generally, items in one resource group are intended to have the same lifecycle, so you can easily deploy, update, and delete them as a group.

1. In the **Location** drop-down menu, ensure that a geographically close location is chosen.

1. Click **OK**.

    At this stage, the Choose a size page appears. Proceed to the next step.

### Step 2: Sizing the new VM and reviewing settings ###

1. On the **Choose A size** page, click **View All**.

    ![ChooseSize](images/ChooseSize.jpg)

1. In the list of available VM types, select **DS1_V2 Standard**. Notice the wide range of VM choices available. Azure provides these according to the growing needs of your workload or application - for example, you might need the high memory allocation (G-series) or additional GPUs (N-series) that maximize the performance of deep-learning experiments (like image classification). Some VMs are most cost-effective for early experiementation (A-series, Dv2 series). A D-series is a good starting point for this lab. You can learn more [here](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-sizes-specs) or [here](https://blogs.msdn.microsoft.com/uk_faculty_connection/2016/09/12/choosing-the-most-appropiate-azure-virtual-machine-specification/).

    ![ONE-002a](images/ONE-002a.jpg "DSVM")

1. Click **Select**.
1. On the **Settings** page that appears, review the default settings, and then click **OK**. The **Create** page appears, displaying offer details and summary information.
1. Click **Create**.
1. Wait a few minutes while the DSVM is deployed. After it is deployed, you will see a dashboard for your new VM. At the top of the dashboard, you will see controls.

    ![StartStop](images/StartStop.jpg "DSVM")

1. The **Start** button is not available, indicating that the new VM has already started.

<a name="Exercise2"></a>

## Exercise 2: Connect to the DSVM ##

In this exercise, you will use a local X2Go client to connect to your new VM in Azure.

1. If you have not already done so, download and install X2Go on your local device. You can download it [here](https://wiki.x2go.org/doku.php/download:start). Instructions [here](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).

    *Check [these docs](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/linux-dsvm-intro#installing-and-configuring-x2go-client) for more information on using X2Go on the Linux Data Science Virtual Machine.*

1. Open X2Go. If you see any security alerts related to firewalls, now or later during these exercises, click **Allow Access**.
1. If the Session Preferences dialog box does not open automatically, from the **Session** menu, click **New Session**.

    ![NewSession](images/NewSession.jpg)

1. Return to your web browser and to the [Azure Portal](https://portal.azure.com), which you opened in Exercise 1.
1. In the **Overview** section, which displays information about the new VM you have created, locate and copy the VM’s public IP address.

    ![ONE-006](images/ONE-006.jpg "DSVM")

1. Return to the **Session Preferences** dialog box in X2Go. Paste the IP address of the Azure VM, which you just copied, into the **Host** field.

    ![SessionPref](images/SessionPref.jpg)

1. In the **Login** field, type the user name you chose for your new VM in Exercise 1.
1. Change the **Session Type** setting to **XFCE**.

    ![17xfce](images/17xfce.jpg "DSVM")

1. Click **OK**.
1. In the X2Go client window, begin a new session by clicking on the name **New session** within the window on the right that displays the IP address you just pasted.

    ![NewSession2](images/NewSession2.jpg)

1. In the **New Session** window, supply the password you specified when you created the VM in Exercise 1, and then click **OK**.

    ![NewSession3](images/NewSession3.jpg)

1. If you see a message displaying a public key hash and asking whether you trust the host key, click **Yes**.

    ![Failed](images/Failed.jpg)

1. Wait a couple of minutes for a connection to the VM to establish. If you are on a Mac and are prompted to install XQuartz, follow the link provided to install XQuartz, log back off and then back on, and finally return to step 10.

    After the connection to the VM completes, a new window opens displaying the VM desktop and then a web page.

    ![NewVM](images/NewVM.jpg)

1. As needed, resize the window displaying the desktop of the DSVM. By default, a webpage is displayed that provides information about the DSVM.
1. Minimize the web browser, take a moment to review the icons on the desktop, and then proceed to Exercise 3.

    ![NewVM2](images/NewVM2.jpg)

<a name="Exercise3"></a>

## Exercise 3: Download a dataset and prepare a Jupyter notebook ##

In this exercise, you will copy a dataset and a Jupyter file from GitHub to a new /notebooks/BnB directory on your DSVM. These files will be used to run machine learning algorithms in a Jupyter notebook in Exercise 4.

Perform the following procedures in the X2Go session window that is connected to the DSVM hosted on Azure.

### Step 1: Copying and decompressing the dataset ###

1. In the DSVM, open a terminal emulator (or shell) by clicking on the appropriate icon at the bottom of the screen.

    ![term](images/term.jpg "DSVM")

1. At the command prompt in the shell, create and switch to a new directory named **BnB** within the **~/notebooks** directory by entering the following three commands, one at a time:

    ```shell
    cd notebooks // change directory to "notebooks"
    mkdir BnB    // make new directory called "BnB"
    cd BnB       // change directory to "BnB"
    ```

    **Note:** On Linux all commands and directory names are case-sensitive.

1. In the DSVM, open a web browser by clicking on the appropriate icon at the bottom of the screen.

    ![Browser](images/Browser.jpg "DSVM")

1. Browse to the following address: <https://github.com/MSFTImagine/computerscience/tree/master/Azure%20University%20Tour/MachineLearningHOL/content>
1. From the list of files in this directory, locate and click **listings.csv.gz**, and then click **Download** and **Save File** to save the file. The file will automatically save to the Downloads directory.
1. From the list of files in this same /content directory, locate and click **iPY_run_annotated.png**, and then click **Download**. Right-click the image in the browser and select **Save Image As**. Choose to save the file in the /notebooks/BnB/ directory.
1. In the DSVM, use **File Manager** (available at the bottom of the screen) to move the **listings.csv.gz** file from the **Downloads** directory to the **/notebooks/BnB/** directory.

    ![FileManager](images/FileManager.jpg "DSVM")

1. The **listings.csv.gz** file now needs to be decompressed. Return to the terminal emulator, and if necessary, use the **cd** command to navigate to the /notebooks/BnB/ directory.
1. Type the following at the command prompt:

    ```shell
    gunzip listings.csv.gz
    ```

    The file **listings.csv** has now replaced **listings.csv.gz** in the /notebooks/BnB/ directory.

### Step 2: Saving the Jupyter Notebook file and opening the notebook ###

In this step, you will save a Jupyter Notebook document from GitHub to the /notebooks/BnB directory in your DSVM.

1. In the DSVM, return to the web browser, and then navigate to <https://github.com/MSFTImagine/computerscience/tree/master/Azure%20University%20Tour/MachineLearningHOL/content>
1. Click **Machine_Learning_HOL_Ex4.ipynb**, click **Raw** in the top-right portion of the screen, and then press Ctrl+S to save the file to the /notebooks/BnB/ directory.

    ![Raw](images/Raw.jpg "DSVM")

    You will now open the Jupyter Notebook web application and then the notebook you just saved.

1. In the DSVM, minimize all active windows. On the desktop, locate and double-click the Jupyter desktop-configuration file.

    ![Jupyter](images/Jupyter.jpg "DSVM")

    A UXTerm terminal opens, and then a webpage opens to <http://localhost:8888/tree.> Inside this webpage, the Jupyter Notebook web application displays the dashboard, which contains a number of directories and Jupyter notebooks.  The Jupyter Notebook dashboard maps to the /notebooks directory within the file structure of the DSVM.

    ![Jupyter2](images/Jupyter2.jpg "DSVM")

1. In the Jupyter Notebook dashboard displayed at <http://localhost:8888,> double-click to open the **/BnB** directory, and then double-click **Machine_Learning_HOL_Ex4.ipynb**.

    ![Jupyter3](images/Jupyter3.jpg "DSVM")

    The Machine_Learning_HOL_Ex4.ipynb notebook will require a few moments to complete loading in the browser.

1. If you receive a Kernel Not Found message, select **Python 3.5** from the drop-down menu, and then click **Set Kernel**.

Once the notebook is fully displayed, continue to Exercise 4.

![Jupyter4](images/Jupyter4.jpg "DSVM")

<a name="Exercise4"></a>

## Exercise 4: Import data, clean data, and make predictions using Python/SciKit-Learn in a Jupyter notebook ##

*This exercise is performed in the Jupyter notebook you opened at the end of Exercise 3. The Jupyter notebook contains all of the instructions for the exercise, including a series of code samples to run.*

**Important: After you done with Exercise 4, be sure to shut down the DSVM in Azure.**

In this exercise, you will import data from the listings.csv file, clean the data, and then build a model to predict the price of a rental property. You should perform this entire exercise in the Jupyter notebook you opened at the end of Exercise 3.

### Stopping the DSVM ###

Before completing the lab, make sure you shut down the virtual machine you created in Microsoft Azure.

1. Close the X2Go window.

1. Return to the [Azure Portal](https://portal.azure.com). Locate and open the settings for the new DSVM that you created as part of this HOL.

1. In the controls, click **Stop** to stop the DSVM.

   ![StartStop2](images/StartStop2.jpg)

This brings us to the end of the Machine Learning HOL. Imagine if you were a professional developer working with increasingly complex models, harnessing [neural network](https://en.wikipedia.org/wiki/Artificial_neural_network) algorithms with deep-learning libraries like Google's [TensorFlow](https://www.tensorflow.org/) and Microsoft's [Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Both Azure's Data Science Virtual Machine supports both libraries, and is available in a GPU-based configuration to maximize performance. Your new AI-powered features provide a competitive advantage in pricing your rental units, enabling your app to attract the best listings which, in turn, are more appealing to price-sensitive users.

Feel free to explore the Jupyter notebook and the environment more. When you are done, remember to return to the Azure Portal and **shut down your DSVM** so you can use your cloud credits in future projects!