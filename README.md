# Persistence-Homology-illustris-simullation-Cosmic-web-
In this project I had used State of the art Topological Data Analysis (TDA).
Afterwards, I had used Gudhi library to find Persistence Homology and then by fiding betti numbers we can have Betti curves. 

## Data Preview

```
fig, ax= plt.subplots(1,3, figsize= [20,6], sharex=True, squeeze=True)

ax[0].imshow(den_0[20])
ax[1].imshow(den_4[20])
ax[2].imshow(den_46[20])
 

ax[0].set_title('z=0')
ax[1].set_title('z=4')
ax[2].set_title('z=46')


plt.show()

```
![download (2)](https://user-images.githubusercontent.com/35627868/182387649-1cc74fc3-3628-419e-a14a-4d76e1988268.png)


## Defining function 

```
def persistence_to_betti (im):
  
  cc = gd.CubicalComplex(top_dimensional_cells=im)
  cc.compute_persistence()
  H0 = cc.persistence_intervals_in_dimension(0)
  H1 = cc.persistence_intervals_in_dimension(1)
  H2 = cc.persistence_intervals_in_dimension(2)


  pd0 = DiagramSelector(use=True, point_type="finite")(H0) 
  bc0 = BettiCurve(resolution=50, sample_range=[-4,4])(pd0)
  pd1 = DiagramSelector(use=True, point_type="finite")(H1) 
  bc1 = BettiCurve(resolution=50, sample_range=[-4,4])(pd1)
  pd2 = DiagramSelector(use=True, point_type="finite")(H2) 
  bc2 = BettiCurve(resolution=50, sample_range=[-4,4])(pd2)

  return bc0 , bc1 , bc2


```


```
def persistence_mean_std(den):
  image = den/den.std()
  image -= image.mean()
  w = 128
  BC0=[]; BC1=[]; BC2=[]
  for i in range(2):  
    for j in range(2):
      for k in range(2):
        im = image[i*w:(i+1)*w,j*w:(j+1)*w,k*w:(k+1)*w]
        BCs = persistence_to_betti(im)   
        BC0.append(BCs[0]) ; BC1.append(BCs[1]) ; BC2.append(BCs[2])
  #Means and std
  bc0_mean = np.mean(BC0, 0) ; bc1_mean = np.mean(BC1, 0) ; bc2_mean = np.mean(BC2, 0)
  bc0_std = np.std(BC0, 0)/7**.5 ; bc1_std = np.std(BC1, 0)/7**.5 ; bc2_std = np.std(BC2, 0)/7**.5
  return bc0_mean, bc1_mean, bc2_mean, bc0_std, bc1_std, bc2_std

```


## calling for images
 
 ```
 
 boxes= []
box_number=[3, 17 ,32 ,41, 54, 68, 85, 103, 135]
zs=        [40, 20, 10, 7, 4, 2, 1, 0.5, 0] #2 7 10


for i in box_number:
  box = gaussian_filter(np.load('256_'+str(i)+'.npz')['density'],2)
  # boxes= np.append(boxes, [box], axis=0)
  boxes.append(box)
 
 ```
 
 
 ```
 fig,ax = plt.subplots(1,3,figsize=(30,10))
plt.suptitle('All Betti curves')

for i,d in enumerate(boxes):
  
  bc0_mean, bc1_mean, bc2_mean, bc0_std, bc1_std, bc2_std = persistence_mean_std(d)
  ax[0].errorbar(x=np.linspace(-4,4,50), y=bc0_mean, yerr=bc0_std, label=f'{zs[i]}');
  ax[1].errorbar(x=np.linspace(-4,4,50), y=bc1_mean, yerr=bc1_std, label=f'{zs[i]}');
  ax[2].errorbar(x=np.linspace(-4,7,50), y=bc2_mean, yerr=bc2_std, label=f'{zs[i]}');
for i in range(3): ax[i].legend()

 ```
 
 ![betti curve](https://user-images.githubusercontent.com/35627868/182386401-c77b4e02-2bac-4ff7-8223-a1d4c6efe2c5.png)
