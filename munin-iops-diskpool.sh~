#!/bin/bash
#Inicializo script bash.

# Modo de uso:
# ./munin-iops-diskpool.sh path_to_directory output

# 1º parte:
# Incluir el directorio donde se encuentran las definiciones de las maquinas virtuales.

#Verifica que el usuario haya ingresado correctamente los parametros de entrada.
#Asigna a variable salida el primer parametro introducido que posee el nombre de archivo de salida. En caso de no existir se crea.
#Si el usuario no ingreso correctamente el modo de uso se  notifica error por pantalla.
if [ $1 ]; then
        salida=$1
else
        echo "[ERROR] Ingrese correctamente el nombre del directorio o del archivo de salida"
        exit 1
fi
#Crea un directorio temporal directory.
directory=$(mktemp -d)
#Ingresa al mismo 
cd $directory
#Realiza copia del repositorio que posee las definiciones de maquinas virtuales, utilizando comando check-out de subversion.
svn co http://noc-svn.psi.unc.edu.ar/servicios/xen

# Elimina directorios innecesarios
rm -rf $directory/xen/suspendidos $directory/xen/.svn $directory/xen/old $directory/xen/error $directory/xen/templates
# archivos para LUN-pool
> navipools.txt
> pools.txt
#archivos para x-vmstore
> result_vm.txt
> result1_vm.txt
> vvmm.txt
> parser_vm.txt
> escrituras_a.txt
> lecturas_a.txt
> escrituras_b.txt
> lecturas_b.txt
> escrituras_c.txt
> lecturas_c.txt
> escrituras_d.txt
> lecturas_d.txt
> escrituras_e.txt
> lecturas_e.txt
> escrituras_f.txt
> lecturas_f.txt
> escrituras_g.txt
> lecturas_g.txt
> escrituras_h.txt
> lecturas_h.txt
> escrituras_i.txt
> lecturas_i.txt
> escrituras_j.txt
> lecturas_j.txt
> escrituras_k.txt
> lecturas_k.txt
> escrituras_l.txt
> lecturas_l.txt
> escrituras_m.txt
> lecturas_m.txt
# archivos par parser dev mapper 
> result_map.txt
> parser_mapp.txt
> escrituras_map.txt
> lecturas_map.txt

# Lee naviseccli.out; archivo creado por el comando:
# # /opt/Navisphere/bin/naviseccli -np -h roman-a.psi.unc.edu.ar  -User USUARIO -Password PASSWORD -Scope 0 getlun -name -rg
# Luego crea un archivo pools.txt que contiene los LUN y a cual Pool pertenece
# Quita toma todas las lineas que no contentan "Logical". Luego elimina lineas en blanco con '^$'. 
# Por ultimo elimina las palabras sobrantes. Dejando solo Pool en una linea y LUN en la linea siguiente. y lo guarda en el archivo navipools.txt

#---Prueba definitiva condicione y descomente  siguientes dos lineas
# /opt/Navisphere/bin/naviseccli -np -h roman-a.psi.unc.edu.ar  -User USUARIO -Password PASSWORD -Scope 0 getlun -name -rg
#egrep -v "LOGICAL" <ruta archivo>/naviseccli.out | grep -v '^$' | sed 's/Name//g;s/RAIDGroup ID://g;s/ *//g' > navipools.txt

#---Prueba en pc de fabiola. Descomentar siguiente linea
#egrep -v "LOGICAL" /home/fabiolac19/Copy/PPS/naviseccli.out | grep -v '^$' | sed 's/Name//g;s/RAIDGroup ID://g;s/ *//g' > navipools.txt
	i1=$(cat navipools.txt | wc -l)
#toma dos lineas conscecutivas y se guardan en linea0 y linea1 para luego ubicarlas juntas en el archivo pools.txt
	for (( c1=1; c1<=i1; c1++ ))
        do
           linea0=$(sed -n -e ${c1}p navipools.txt)
           (( d1=c1+1 ))
           linea1=$(sed -n -e ${d1}p navipools.txt)
# Verificamos que no sean dos lineas iguales comparando los primeros 5 caracteres. Tambien tenemos en cuenta si la linea siguiente es nula.
           if [[ "$(echo $linea0 | cut -c1-5)" = "$(echo $linea1 | cut -c1-5)" ||  -z "$linea1" ]]; then
                echo "Se repiten o lineaN1 es NULL" > /dev/null
           else
                echo $linea0 $linea1 >> pools1.txt
                (( c1=c1+1 ))
           fi
        done
# Eliminamos lineas que contengan los siguientes caracteres:
# "__" debido a las lineas: __METALUN-60:06:01:60:5A:27:2A:00:E8:DF:B0:9F:D0:F6:E1:11-1019
# "Spare" debido a los ...........
# "N/A" por ejemplo: LUN: 4, Name: vsinapsis-pool, RAIDGroup ID: N/A. Debido a pool Not Available n/a
egrep -v "__|Spare|N/A" pools1.txt  > pools.txt

# Lee archivo por archivo, los descriptores de las maquinas virtuales que se encuentran en el directorio xen, y los guarda en vvmm.txt
for file in $directory/xen/*; do
        while read -r line; do
        echo "$line" >> vvmm.txt
        done < $file

# Toma de vvmm.txt las vm vmstore y su correspondiente disco y lo guarda en result1_vm.txt
# El mismo queda de la forma: xvda vmstore-c databases-3 xvda
	egrep "dev|vmstore" vvmm.txt | egrep -v "rogelio|device|args|description|\(name|phy:" > parser_vm.txt
	iv=$(cat parser_vm.txt | wc -l)
	for (( cv=1; cv<=iv; cv++ ))
        do
           lineaN0_vm=$(sed -n -e ${cv}p parser_vm.txt)
           (( dv=cv+1 ))
           lineaN1_vm=$(sed -n -e ${dv}p parser_vm.txt)
           if [[ "$(echo $lineaN0_vm | cut -c1-5)" = "$(echo $lineaN1_vm | cut -c1-5)" ||  -z "$lineaN1_vm" ]]; then
                echo "Se repiten o lineaN1_vm es NULL" > /dev/null
           else
                echo $lineaN0_vm $lineaN1_vm | sed 's/(dev //g;s/(//g;s/)//g;s/\:disk//g;s/uname//g;s/file\:\/srv\/xen\///g;s/)//g;s/\// /g;s/  / /g' >> result1_vm.txt
                (( cv=cv+1 ))
           fi
        done

# Toma de vvmm.txt las vm mapper y su correspondiente disco y lo guarda en result_map.txt
# El mismo queda de la forma: alfresco xvde alfresco-opt
	egrep "dev|mapper" vvmm.txt | egrep -v "rogelio|device|args|description|file|\(name" > parser_mapp.txt
	im=$(cat parser_mapp.txt | wc -l)
	for (( cm=1; cm<=im; cm++ ))
	do
	   lineaN0_mapp=$(sed -n -e ${cm}p parser_mapp.txt)
	   (( dm=cm+1 ))
	   lineaN1_mapp=$(sed -n -e ${dm}p parser_mapp.txt)
	   if [[ "$(echo $lineaN0_mapp | cut -c1-5)" = "$(echo $lineaN1_mapp | cut -c1-5)" ||  -z "$lineaN1_mapp" ]]; then
		echo "Se repiten o lineaN1_mapp es NULL" > /dev/null
	   else
		echo $file $lineaN0_mapp $lineaN1_mapp | sed 's/(dev //g;s/(//g;s/)//g;s/\:disk //g;s/uname//g;s/phy\:\/dev\/mapper\///g;s/.*\///g' >> result_map.txt
		(( cm=cm+1 ))
	   fi
	done
> vvmm.txt
done
sed '/anc-aromo-2/d;/srv-ubuntu14-dev/d' result1_vm.txt >> result_vm.txt

# Seccion que se encarga de tomar las escrituras y lecturas de todos los vmstore a b c d ... m 
while read -r line; do
	#recordando el ejemplo: xvda vmstore-c databases-3 xvda
    vmss=$(echo $line | cut -f3 -d' ')		# vmss: databases-3
    diskvm=$(echo $line | cut -f1 -d' ')	# diskvm: xvda
	#res_vm=$(echo $line | cut -f2-4 -d' ' | sed 's/ /_/g')
	vmm=$(echo $line | cut -f2 -d' ') 		# vmm: vmstore-c
# Busca en el archivo de configuracion munin la ruta de ubicacion de la vm
# Toma una linea y corta todo lo anterior al caracter [ y elimina el caracter ] al final
	plinevm=$(echo $(egrep "\;$vmss\.unc\.edu\.ar\]$|\;$vmss\.psi\.unc\.edu\.ar\]$|\;$vmss\]$" /etc/munin/munin.conf | head -n1 | sed 's/^.*\[//g;s/\]//g'))
        if [[ -z $plinevm ]]; then
                echo $plinevm > /dev/null
        else
		if [ "$vmm" = "vmstore-a" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_a.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_a.txt 
	        fi
		if [ "$vmm" = "vmstore-b" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_b.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_b.txt
	        fi
		if [ "$vmm" = "vmstore-c" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_c.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_c.txt
	        fi
		if [ "$vmm" = "vmstore-d" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_d.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_d.txt
	        fi
		if [ "$vmm" = "vmstore-e" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_e.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_e.txt
	        fi
		if [ "$vmm" = "vmstore-f" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_f.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_f.txt
	        fi
		if [ "$vmm" = "vmstore-g" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_g.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_g.txt
	        fi
		if [ "$vmm" = "vmstore-h" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_h.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_h.txt
	        fi
		if [ "$vmm" = "vmstore-i" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_i.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_i.txt
	        fi
		if [ "$vmm" = "vmstore-j" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_j.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_j.txt
	        fi
		if [ "$vmm" = "vmstore-k" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_k.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_k.txt
	        fi
		if [ "$vmm" = "vmstore-l" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_l.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_l.txt
	        fi
		if [ "$vmm" = "vmstore-m" ]
		then
	                echo "$plinevm:diskstats_iops.$diskvm.wrio" >> escrituras_m.txt
                	echo "$plinevm:diskstats_iops.$diskvm.rdio" >> lecturas_m.txt
	        fi
	fi
done < result_vm.txt

# Leemos el archivo pools.txt el cual contiene como primer campo la LUN y como segundo campo el pool al que corresponde.
while read -r line; do
    lun=$(echo $line | cut -f1 -d' ')
	pool=$(echo $line | cut -f2 -d' ')

# Leemos archivo que contiene las vm mapper y buscamos las LUN que necesitamos
	while read -r line; do
		# Recordando el ejemplo anterior: alfresco xvde alfresco-opt
		vm=$(echo $line | cut -f1 -d' ')	#vm: alfresco
		disk=$(echo $line | cut -f2 -d' ')	#disk: xvde
		res=$(echo $line | cut -f3 -d' ') 	#res: alfresco-opt
		pline=$(echo $(egrep "$vm\.|$vm\]" /etc/munin/munin.conf | head -n1 | sed 's/^.*\[//g;s/\]//g'))
		if [ -z $pline ]; then
			echo $pline > /dev/null
		else
			if [ "$lun" = "$res" ]
			then
			# Agrupamos las LUN por pool. Guardamos lecturas y escrituras en sus correspondientes archivos
				if [ "$pool" = "0" ] 
				then		
					echo "$pline:diskstats_iops.$disk.wrio" >> pool0_esc.txt
					echo "$pline:diskstats_iops.$disk.rdio" >> pool0_lec.txt
				fi
				if [ "$pool" = "1" ] 
				then
					echo "$pline:diskstats_iops.$disk.wrio" >> pool1_esc.txt
					echo "$pline:diskstats_iops.$disk.rdio" >> pool1_lec.txt
				fi
				if [ "$pool" = "2" ] 
				then
					echo "$pline:diskstats_iops.$disk.wrio" >> pool2_esc.txt
					echo "$pline:diskstats_iops.$disk.rdio" >> pool2_lec.txt
				fi
				if [ "$pool" = "3" ] 
				then
					echo "$pline:diskstats_iops.$disk.wrio" >> pool3_esc.txt
					echo "$pline:diskstats_iops.$disk.rdio" >> pool3_lec.txt
				fi
				if [ "$pool" = "4" ] 
				then
					echo "$pline:diskstats_iops.$disk.wrio" >> pool4_esc.txt
					echo "$pline:diskstats_iops.$disk.rdio" >> pool4_lec.txt
				fi
			fi						
		fi
	done < result_map.txt		

# Agrupamos las LUN vmstore por pool. 
# Tomamos las sumas de las lecturas y escrituras de la correspondiente vmstore obtenidas anteriormente 
# y la agregamos al archivo de lectura y escritura de cada pool
if [ "$lun" = "vmstore-a" ]
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_a.txt >> pool0_lec.txt
			cat escrituras_a.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_a.txt >> pool1_lec.txt
			cat escrituras_a.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_a.txt >> pool2_lec.txt
			cat escrituras_a.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_a.txt >> pool3_lec.txt
			cat escrituras_a.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_a.txt >> pool4_lec.txt
			cat escrituras_a.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-b" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_b.txt >> pool0_lec.txt
			cat escrituras_b.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_b.txt >> pool1_lec.txt
			cat escrituras_b.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_b.txt >> pool2_lec.txt
			cat escrituras_b.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_b.txt >> pool3_lec.txt
			cat escrituras_b.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_b.txt >> pool4_lec.txt
			cat escrituras_b.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-c" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_c.txt >> pool0_lec.txt
			cat escrituras_c.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_c.txt >> pool1_lec.txt
			cat escrituras_c.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_c.txt >> pool2_lec.txt
			cat escrituras_c.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_c.txt >> pool3_lec.txt
			cat escrituras_c.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_c.txt >> pool4_lec.txt
			cat escrituras_c.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-d" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_d.txt >> pool0_lec.txt
			cat escrituras_d.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_d.txt >> pool1_lec.txt
			cat escrituras_d.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_d.txt >> pool2_lec.txt
			cat escrituras_d.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_d.txt >> pool3_lec.txt
			cat escrituras_d.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_d.txt >> pool4_lec.txt
			cat escrituras_d.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-e" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_e.txt >> pool0_lec.txt
			cat escrituras_e.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_e.txt >> pool1_lec.txt
			cat escrituras_e.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_e.txt >> pool2_lec.txt
			cat escrituras_e.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_e.txt >> pool3_lec.txt
			cat escrituras_e.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_e.txt >> pool4_lec.txt
			cat escrituras_e.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-f" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_f.txt >> pool0_lec.txt
			cat escrituras_f.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_f.txt >> pool1_lec.txt
			cat escrituras_f.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_f.txt >> pool2_lec.txt
			cat escrituras_f.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_f.txt >> pool3_lec.txt
			cat escrituras_f.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_f.txt >> pool4_lec.txt
			cat escrituras_f.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-g" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_g.txt >> pool0_lec.txt
			cat escrituras_g.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_g.txt >> pool1_lec.txt
			cat escrituras_g.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_g.txt >> pool2_lec.txt
			cat escrituras_g.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_g.txt >> pool3_lec.txt
			cat escrituras_g.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_g.txt >> pool4_lec.txt
			cat escrituras_g.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-h" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_h.txt >> pool0_lec.txt
			cat escrituras_h.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_h.txt >> pool1_lec.txt
			cat escrituras_h.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_h.txt >> pool2_lec.txt
			cat escrituras_h.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_h.txt >> pool3_lec.txt
			cat escrituras_h.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_h.txt >> pool4_lec.txt
			cat escrituras_h.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-i" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_i.txt >> pool0_lec.txt
			cat escrituras_i.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_i.txt >> pool1_lec.txt
			cat escrituras_i.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_i.txt >> pool2_lec.txt
			cat escrituras_i.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_i.txt >> pool3_lec.txt
			cat escrituras_i.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_i.txt >> pool4_lec.txt
			cat escrituras_i.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-j" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_j.txt >> pool0_lec.txt
			cat escrituras_j.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_j.txt >> pool1_lec.txt
			cat escrituras_j.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_j.txt >> pool2_lec.txt
			cat escrituras_j.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_j.txt >> pool3_lec.txt
			cat escrituras_j.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_j.txt >> pool4_lec.txt
			cat escrituras_j.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-k" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_k.txt >> pool0_lec.txt
			cat escrituras_k.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_k.txt >> pool1_lec.txt
			cat escrituras_k.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_k.txt >> pool2_lec.txt
			cat escrituras_k.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_k.txt >> pool3_lec.txt
			cat escrituras_k.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_k.txt >> pool4_lec.txt
			cat escrituras_k.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-l" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_l.txt >> pool0_lec.txt
			cat escrituras_l.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_l.txt >> pool1_lec.txt
			cat escrituras_l.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_l.txt >> pool2_lec.txt
			cat escrituras_l.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_l.txt >> pool3_lec.txt
			cat escrituras_l.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_l.txt >> pool4_lec.txt
			cat escrituras_l.txt >> pool4_esc.txt
		fi
	fi	
	if [ "$lun" = "vmstore-m" ] 
	then
		if [ "$pool" = "0" ] 
		then		
			cat lecturas_m.txt >> pool0_lec.txt
			cat escrituras_m.txt >> pool0_esc.txt
		fi
		if [ "$pool" = "1" ] 
		then
			cat lecturas_m.txt >> pool1_lec.txt
			cat escrituras_m.txt >> pool1_esc.txt
		fi
		if [ "$pool" = "2" ] 
		then
			cat lecturas_m.txt >> pool2_lec.txt
			cat escrituras_m.txt >> pool2_esc.txt
		fi
		if [ "$pool" = "3" ]
		then
			cat lecturas_m.txt >> pool3_lec.txt
			cat escrituras_m.txt >> pool3_esc.txt
		fi
		if [ "$pool" = "4" ]
		then
			cat lecturas_m.txt >> pool4_lec.txt
			cat escrituras_m.txt >> pool4_esc.txt
		fi
	fi
done < pools.txt

> $salida

# Inicializa archivo salida con configuraciones de etiqueta y demas, propias de Munin como:
# diskstats_iops.graph_title IOPS Lecturas por pool 	definimos Titulo: IOPS Lecturas por pool
# diskstats_iops.graph_vlabel IOs/sec                	eje y: IOs por segundo
# diskstats_iops.p0.label pool0_lec		              	declaramos etiquetas pool0... para p0 p1 p2 p3 p4 correspondientemente
# diskstats_iops.graph_order p0 p1 p2 p3 p4	      		definimos orden 
# diskstats_iops.p0.sum 								sumamos los iops que queremos graficar. Brindandole el archivo correpondiente a las lecturas o escrituras de cada pool

echo "[UNC;PSI;NOC;Infraestructura;Storage;EMC;diskpool]"	        >> $salida
echo "    update no"                                                    >> $salida
echo "    diskstats_iops.update no"                                     >> $salida
echo "    diskstats_iops.graph_title IOPS Lecturas por pool"	        >> $salida
echo "    diskstats_iops.graph_category IOPS"                           >> $salida
echo "    diskstats_iops.graph_args --base 1000"                        >> $salida
echo "    diskstats_iops.graph_vlabel IOs/sec"                          >> $salida
echo "    diskstats_iops.p0.label pool 0"		                >> $salida
echo "    diskstats_iops.p1.label pool 1"		                >> $salida
echo "    diskstats_iops.p2.label pool 2" 		                >> $salida
echo "    diskstats_iops.p3.label pool 3"		                >> $salida
echo "    diskstats_iops.p4.label pool 4"		                >> $salida
echo "    diskstats_iops.graph_order p0 p1 p2 p3 p4"	                >> $salida
echo "    	diskstats_iops.p0.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool0_lec.txt
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool1..................................................................
echo "    	diskstats_iops.p1.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool1_lec.txt
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool2..................................................................
echo "    	diskstats_iops.p2.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool2_lec.txt
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool3..................................................................
echo "    	diskstats_iops.p3.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool3_lec.txt
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool4..................................................................
echo "    	diskstats_iops.p4.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool4_lec.txt
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#.........................ESCRITURAS...............................................
echo " "                                                                >> $salida
echo "    diskstats_iops_1.update no"                                   >> $salida
echo "    diskstats_iops_1.graph_title IOPS Escrituras por pool"	>> $salida
echo "    diskstats_iops_1.graph_category IOPS"                         >> $salida
echo "    diskstats_iops_1.graph_args --base 1000"                      >> $salida
echo "    diskstats_iops_1.graph_vlabel IOs/sec"                        >> $salida
echo "    diskstats_iops_1.pp0.label pool 0"		                >> $salida
echo "    diskstats_iops_1.pp1.label pool 1"		                >> $salida
echo "    diskstats_iops_1.pp2.label pool 2"		                >> $salida
echo "    diskstats_iops_1.pp3.label pool 3"		                >> $salida
echo "    diskstats_iops_1.pp4.label pool 4"		                >> $salida
echo "    diskstats_iops_1.graph_order pp0 pp1 pp2 pp3 pp4"             >> $salida
echo "    	diskstats_iops_1.pp0.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool0_esc.txt 
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool1..................................................................
echo "    	diskstats_iops_1.pp1.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool1_esc.txt 
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool2..................................................................
echo "    	diskstats_iops_1.pp2.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool2_esc.txt 
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool3..................................................................
echo "    	diskstats_iops_1.pp3.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool3_esc.txt 
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida

#............pool4..................................................................
echo "    	diskstats_iops_1.pp4.sum \\"           			>> $salida
while read -r line; do
        echo "          $line \\"                                       >> $salida
done < pool4_esc.txt 
        line=$(tail -n1 $salida | sed 's/\\//g')
        sed '$ d' $salida                                               >  temp.txt
        cat temp.txt                                                    >  $salida
        echo "$line"                                                    >> $salida


#Movemos salida al directorio de munin
mv $salida /etc/munin/munin-conf.d/$salida
#Borramos archivos temporales
rm -rf $directory 
