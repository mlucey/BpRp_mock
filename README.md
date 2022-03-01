# BpRp_mock


Mock Gaia DR3 Bp & Rp spectra created by Maddie Lucey. Spectral synthesis was done using Turbospectrum. The spectrum is then convolved to a resolution of 70 and pixels are resampled according to https://www.cosmos.esa.int/web/gaia/resolution. We also add extinction using the astropy dust_extinction package (https://dust-extinction.readthedocs.io/en/stable/#) as well as Poisson noise (to be added). 

Please reach out to me for more information if you are using these spectra prior to Gaia DR3 (m_lucey@utexas.edu). Quickly after DR3 I hope to have a paper people can cite where the full description of the mock spectra will be. 

extinct_grid.tar.gz contains a grid of 76,260 spectra with effective temperature between 3000 K and 6000 K in steps of 500 K, surface gravity between 0 dex and 5 dex in steps of 0.5 dex, metallicity between 0.5 dex and -5 dex in steps of 0.5 dex, [C/Fe] abundances between 2 dex and 0 dex in steps of 0.25 dex and extinction (A_v) betweeon 10 mag and 0 mag with steps of 1 mag. As the atmosphereic models do not exist for some of these grid points, they do not have spectra. The column 'synth' gives a boolean indicating whether or not the synthesis was successful. 
extinct_test.tar.gz contains 1,300 spectra uniformly sampled between the grid points described above. 


The file synth_extinct.fits contains the effective temperature, surface gravity, metallicity ([Fe/H]), carbon abundance ([C/Fe]) and extinction (A_v) of each star in the extinct_grid.tar.gz file. The column synth is a boolean value describing whether the synthesis was successfully created for those parameters. synth_extinct_test.tar.gz is the equivalent file for the spectra in extinct_test.tar.gz.


Below is a snippet of code for reading in all of the spectra into a single array where the spectra are normalized from 0-1



 	t = Table.read('/home/mrl2968/Desktop/CEMP/synth_test_grid.fits')
	t = t[np.where(t['synth'])[0]]
	for i in range(len(t)):		
		Teff = t['Teff'][i]
		logg = t['logg'][i]
		feh = t['feh'][i]
		c = t['c_fe'][i]
   		 a = t['A_v'][i]

		filename = '%s_g%s_f%s_c%s_a%s.txt'%(str(Teff),str(logg),str(feh),str(c),str(a)


		w, fbp = np.loadtxt('/home/mrl2968/Desktop/CEMP/synth_spec/test/Bp_'+filename,usecols=(0,1),skiprows=1,unpack=True)
		wr, frp = np.loadtxt('/home/mrl2968/Desktop/CEMP/synth_spec/test/Rp_'+filename,usecols=(0,1),skiprows=1,unpack=True)

		f = np.concatenate([fbp,frp])
		w = np.concatenate([w,wr])
		if i == 0:
			all_x = np.zeros((len(t),len(f)),dtype='float')
			wavs = w
		all_x[i] = f/np.nanmax(f)
