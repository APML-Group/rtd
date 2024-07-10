Welcome to documentation for the adaptive-partitioning multilayer method!
=========================================================================

Purpose

The adaptive-partitioning multilayer (APML) method, also known as a multi-layer mixed QM/MM method, is a multiscale modeling approach developed for modeling diffuse chemical systems.


Theory

APML works by dividing an entire system into multiple layers. Each layer is described with a different level of theory. More important layers are described with a higher level of theory, while less important layers are approximated. The layers are usually centered around a given substrate or ion of interest. 

*** Add diagram of simple APML system ***

The layers, from inner to outer most, are described as follows:

•	Q1: Uses high level quantum mechanics to describe short-range interactions between substrate/ion and first solvation shell
•	Q2: Uses low level quantum mechanics to describe mid-range interactions between substrate/ion and second solvation shell
•	MM: Uses molecular mechanics to approximate long-range interactions between substrate/ion and remaining environment

A buffer zone exists between the Q1/Q2 and Q2/MM layers to smoothly interpolate the energies and/or forces as molecules move dynamically between each layer. 

Smoothing between Q2/MM employs the same algorithms as the two-layer adaptive QM/MM model, while smoothing between Q1/Q2 uses the permuted-adaptive partitioning (PAP) method or the more recently developed interpolated-adaptive partitioning (IAP) method.

PAP uses a weighted many bodies expansion of the total energy of a model system. In contrast, IAP uses an “alchemical” approach with mixed basis sets, Fock matrices, and overlap matrices. Both the weights and degrees of mixing (Pi) in IAP and PAP depend on the locations of molecules/fragments within the Q1/Q2 buffer layer. The Pi values are described as follows:

•	Pi = 1; fragment in Q1
•	Pi = 0; fragment in Q2
•	1 > Pi > 0; fragment in buffer

Both PAP and IAP produce smooth potential energy surfaces. Neither method is “more correct” in theory than the other. IAP can be more computationally efficient for large systems, while PAP can be efficient if run in parallel.


Work Flow

*** Add diagram of APML work flow ***

APML uses QMMM as a driver to run molecular dynamic (MD) simulations. QM calculations are run using GAMESS (General Atomic and Molecular Electronic Structure System). MM calculations are run using AMBER, CHARM, or OpenMM.


Input

QCSchema – JSON schema for defining fragments in APML system.

User will provide the following:

schema_name: TODO:	

schema_version: TODO:
•	OPTIONS: 
    - DEFAULT: 1

system_name: Name for chemical system being modeled. 
•	OPTIONS:
    - No restrictions

fragments: All individual molecules in chemical system 
•	name: Unique identifier for individual molecule. 
  •	OPTIONS:
    - DEFAULT: Index
    - Name

•	group: Layer where molecule is currently located. 
  •	OPTIONS:
    - “q1”
    -	“q1_q2_buffer”
    -	“q2”
    -	“q2_mm_buffer”
    -	“mm”

•	geometry: Cartesian (x,y and z) coordinates of each atom in a molecule
  •	OPTIONS:
    -	DEFAULT: angstroms

•	symbols: Atomic symbol for each element in a molecule
  •	OPTIONS:
    -	Must be a valid symbol

•	charge: Overall charge of molecule
  •	OPTIONS:
    -	0
    -	1
    -	-1

•	spin: TODO:

driver:	
•	OPTIONS: 
    - DEFAULT: “md”

model:
•	q1_method: High-level quantum method for describing molecules in Q1 layer
  •	OPTIONS:
    -	HF
    -	DFT

•	q1_basis: High-level basis set for describing molecules in Q1 layer
  •	OPTIONS:

•	q2_method: Low-level quantum method for describing molecules in Q2 layer
  •	OPTIONS:
    - HF
    -	DFT
    -	semi-empirical

•	q2_basis: Low-level basis set for describing molecules in Q2 layer
  •	OPTIONS:

keywords:
•	mm_program: TODO:
  •	OPTIONS:
    - DEFAULT: QMMM

•	qc_program: TODO:
  •	OPTIONS:
    -	DEFAULT: GAMESS

•	md_steps: Total number of steps for running molecular dynamic simulations
  •	OPTIONS:

•	apml_method: Method used to interpolate energy and/or forces in buffer layers
  •	OPTIONS:
    -	“iap”
    -	“pap”

•	weight_fn: Weight applied to each many-body terms
  •	OPTIONS:
    -	DEFAULT: “polynomial”

•	pap_mbe_order: Truncation of many-body terms
  •	OPTIONS:
    -	1
    -	DEFAULT: 2
    -	3

•	q1_keywords: Options for controlling calculations in Q1 layer
  o	scf_type: TODO:
    •	OPTIONS:
      -	“df”

  o	e_convergence: TODO:
    •	OPTIONS:
      -	DEFAULT: 0.0000001
  
  o	d_convergence: TODO:
    •	OPTIONS:
      -	DEFAULT: 0.0000001

•	q2_keywords: Options for controlling calculations in Q2 layer
  o	scf_type:
    •	OPTIONS:
      -	DEFAULT: “diis”
  
  o	e_convergence:
    •	OPTIONS:
      -	DEFAULT: 0.0000001
  
  o	d_convergence:
    •	OPTIONS:
      -	DEFAULT: 0.0000001


Outputs

Files:
•	QMMM log file

Graphs:
•	Total Energy (IAP/PAP)
•	Interaction Energy (IAP/PAP)
•	1-Body Total Energy (PAP)
•	2-Body Total Energy (PAP)
•	3-Body Total Energy (PAP)
•	Center of gradients (IAP/PAP)
•	Trajectories (IAP/PAP)


Progress

Currently, supports PAP MD simulations.
Under development: IAP MD simulations, many-layer (>3) functionality.


Software

Supported MM: QMMM, OpenMM

QMMM: https://comp.chem.umn.edu/qmmm/
*** Need link to CU Denver’s version of QMMM ***

Supported QM: GAMESS, Psi4

GAMESS: https://www.msg.chem.iastate.edu/gamess/


Hardware

Currently, CPU only. Under development, GPU support.


GitHub Link

TODO:


Funding

•	This work is supported by the National Science Foundation (CHE-2153441) and National Institute of General Medical Sciences (1R15GM141728-01). 
•	This work is also supported by the University of Colorado Denver. Calculations were run on the CU Denver Alderaan cluster, supported by NSF (2019089).


Papers

1.	Mato, J.; Duster, A. W.; Guidez, E. B.; Lin, H. Adaptive-Partitioning Multilayer Dynamics Simulations: 1. On-the-Fly Switch between Two Quantum Levels of Theory. J. Chem. Theory Comput. 2021, 17 (9), 5456-5465. DOI: 10.1021/acs.jctc.1c00556 

2.	Tran, A. L.; Guidez, E. B.; Lin, H. Adaptive-Partitioning Multilayer Dynamics Simulations: 2. Implementations of the Permuted and Interpolated Adaptive-Partitioning Gradients. J. Phys. Chem. A. 2023, 127 (48), 10320-10333. DOI: 10.1021/acs.jpca.3c05600 


Contact

Bryce Westheimer
Bryce.westheimer@ucdenver.edu

Emilie Guidez
Emilie.guidez@ucdenver.edu

Hai Lin:
Hai.lin@ucdenver.edu


Contributors

Anh Tran, M.S
Julia Clara-Roma, M.S
Faith Montemayor, B.S

Dr. Bryce Westheimer, PhD
Dr. Joani Mato, PhD
Dr. Adam Duster, PhD

Dr. Emilie Guidez, PhD
Dr. Hai Lin, PhD


