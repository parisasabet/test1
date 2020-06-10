%let report1a_attributes=
			npi flu_season entity_type pos_category /*these are the primary keys, rest are attributes*/
			hcp_first_name
			HCP_Last_Name
			Organization_Name
			Healthcare_Provider_Name
			Practice_Address
			Practice_City
			Practice_State
			Practice_ZIP
			NPPES_Primary_Taxonomy
			nppes_primary_taxonomy_text
			Active_ZIP_Flag
			Graduation_Year
			HCP_Year_of_Birth
			HCP_Gender
			aco_patient_flag
		    SNF_patient_count
		 	all_patient_count
		 	em_patient_count
			flu_admin_patient_count
			flu_admin_G0008_patient_count
		 	flu_admin_G8482_patient_count
			flu_std_patient_count
			fluzone_patient_count
		 	fluad_patient_count
			flublok_patient_count
		 	flucelvax_patient_count
		 	/*these variables were for qc only
		 	flu_admin_doc_patient_count
		 	flu_shot_patient_count
		  	flu_brand19_20_patient_count
		 	flu_brand18_19_patient_count*/
;
data sh027654.c_&project_name._report1a (keep = &report1a_attributes);	
	RETAIN &report1a_attributes;
set sh027654.c._&project_name._report1a	;
*rename other variables at request of qa;
SNF_patient_count=eval_manage_SNF_patient_count;
em_patient_count=eval_manage_patient_count;
*there are some missing entity type --delete OR list them as unknown/deactivated;
	if entity_type in(' ','Unknown - Deactivated') then delete;
*label those who did not link to aco file;
if all_patient_count_aco_2019q1=. then all_patient_count_aco_2019q1=0;
if all_patient_count_aco_2019q1=. then aco_patient_flag=0;
*label those with active zip missing as 0;
if Active_ZIP_Flag=. then Active_ZIP_Flag=0;
*delete those with all_patient_count<11--added by QA;
IF all_patient_count < 11 then delete;
*make variables between 0 and 10 negative 6;
   array change all_patient_count SNF_patient_count em_patient_count flu:;
        do over change;
        	/*if count<11 change to -6 even true zeroes---added by QA*/
            IF change < 11 then change=-6;
        end;
*labels to make variables with spaces in them;
label npi='HCP/HCO NPI';
label flu_season="Flu Season";
label entity_type='Entity Type';
label pos_category="POS Category (Pharmacy, Office, Other)";
label HCP_First_Name="NPI First Name";
label HCP_Last_Name="NPI Last Name";
label Organization_Name="NPI Organization Name";
label Healthcare_Provider_Name="Healthcare Provider Name (First/Last Name or Organization Name)";
label Practice_Address="Practice Address";
label Practice_City="Practice City";
label Practice_State="Practice State";
label Practice_ZIP="Practice ZIP";
label nppes_primary_taxonomy='NPI NPPES Primary Taxonomy';
label nppes_primary_taxonomy_text='NPI NPPES Primary Taxonomy Description';
label Active_ZIP_Flag="Active ZIP Flag";
label Graduation_Year="Graduation Year";
label HCP_Year_of_Birth="HCP Year of Birth";
label HCP_Gender="HCP Gender";	*note that gender is different in Medicare vs NPPES--in Medicare 1=male;
label aco_patient_flag='ACO Patient Flag';
label SNF_patient_count="SNF Patient Count";
label all_patient_count='All Patient Count';
label em_patient_count="E&M Patient Count";
label flu_admin_patient_count="Flu Admin Code Group Patient Count";
label flu_admin_G0008_patient_count="Flu Admin (G0008) Patient Count";
label flu_admin_G8482_patient_count="Flu Admin (G8482) Patient Count";
label flu_admin_doc_patient_count="Flu Admin (Documentation) Patient Count";
label flu_std_patient_count="Flu STD Code Group Patient Count";
label fluzone_patient_count="Fluzone HD Code Group Patient Count";
label fluad_patient_count="Fluad Code Group Patient Count";
label flublok_patient_count="FluBlock Code Group Patient Count";
label flucelvax_patient_count="Flucelvax STD Code Group Patient Count";
run;
Here is the code to export:
%export_table_labels(library=SH027654,table=c_sny_vaccine_mv_report1a,dst_dir=Phu);*parisa the sny vaccine prt might be wrong--please edit.  i wrote this from memory.
