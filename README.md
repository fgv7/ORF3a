# ORF3a_Water_MD.mdp
gmx pdb2gmx -f 6xdc.pdb -o 6xdc.gro -p 6xdc.top -ignh
gmx pdb2gmx -f 6xdc.pdb -o 6xdc.gro -p 6xdc.top -ignh -his
gmx editconf -f protein_processed.gro -o protein_newbox.gro -bt dodecahedron -c -d 0.8
gmx solvate -cp protein_newbox.gro -cs spc216.gro -o protein_solv.gro -p 6xdc.top
gmx grompp -f minim-steep.mdp -c protein_solv.gro -p 6xdc.top -o ions.tpr -maxwarn 1
echo 13 | gmx genion -s ions.tpr -o protein_solv_ions.gro -neutral -conc 0.15 -p 6xdc.top
gmx grompp -f minim-steep.mdp -c protein_solv_ions.gro -p 6xdc.top -o minim-steep.tpr
gmx mdrun -v -deffnm minim-steep
gmx grompp -f minim-cg.mdp -c minim-steep.gro -p 6xdc.top -o minim-cg.tpr
gmx mdrun -v -deffnm minim-cg 
gmx grompp -f nvt.mdp -c minim-cg.gro -p 6xdc.top -o nvt.tpr -r minim-cg.gro -maxwarn 10
gmx mdrun -v -deffnm nvt -ntmpi 4
gmx grompp -f npt.mdp -c nvt.gro -p 6xdc.top -o npt.tpr -r nvt.gro -maxwarn 10
gmx mdrun -v -deffnm npt -ntmpi 4
gmx grompp -f md.mdp -c npt.gro -p 6xdc.top -o md.tpr -maxwarn 2
gmx mdrun -v -deffnm md -ntmpi 4




