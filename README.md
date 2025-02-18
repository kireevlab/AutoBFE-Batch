# BindingFreeEnergy-ScriptsInBatchMode-LinuxPlatform

**The workflow on MD-based free energy calculation:**
![image](https://github.com/user-attachments/assets/89bd4b4a-b22a-4334-b788-4a94d79c9407)

**Installation**
1. Gaussian v16
2. AMBER v22
3. Schrödinger v2024
4. GROMACS v2022
5. gmx_MMPBSA v1.6.x

## **A. Preprocessing**  
Download all compressed files and upload to the terminal, and decompressed through: `tar -zxvf filenamehere.tar.gz`

### **A1. Ligand Parameterization (Working Directory: `LigPara`)**  
1. **Generate ESP Charges**  
   - Open a terminal and navigate to the `ESP` folder and run:  
     ```bash
     ./submit_esp.sh PDBbind_db_ligands.sdf 0
     ```  
   - **Note:** It is recommended to group ligands with the same net charge into a single SDF file. Here, `0` represents the net charge of the system. 

2. **Perform RESP Charge Fitting**  
   - After the ESP calculation is complete, move all *.log files into `RESP` folder then go to the `RESP` folder and run:  
     ```bash
     sbatch submit_resp.sh
     ```

### **A2. Protein Parameterization (Working Directory: `ProtPara`)**  
1. **Prepare Protein Structures**  
   - Use the **Protein Preparation Workflow** in **Maestro**.  
   - Save prepared protein structures into the `prepared_proteins/` directory.  

2. **Generate Complex Structures & Topology Files in Batch Mode**  
   - Move all newly generated sub-folders from `RESP` here.
   - Run the following command:  
     ```bash
     sbatch submit_proteintop.sh
     ```  
   - **Note:** Separate complexes **with** and **without ions** into different folders.  

---

## **B. MD Simulation & Free Energy Calculation**  

### **B1. Molecular Dynamics (MD) Simulation**  
- Move all newly generated sub-folders from `ProtPara` here.
- Submit MD simulations in batch mode on **GPU** (Working Directory: `MD`):  
  ```bash
  sbatch Gpu-submit.sh
  ```

### **B2. Free Energy Calculations**  

1. **MM/PBSA & MM/GBSA Calculations**  
   - Run in batch mode on **CPU** (Working Directory: `MMPBSA`):  
     ```bash
     sbatch rmsd-gmxmmpbsa.sh
     ```

2. **Alchemical Free Energy Calculations (FEP)**  
   - Submit Free Energy Perturbation (FEP) calculations (Working Directory: `FEP`):  
     ```bash
     sbatch FEP-submit.sh
     ```  
   - Run **BAR (Bennett Acceptance Ratio) Analysis**:  
     ```bash
     sbatch barAnalysis.sh
     ```
