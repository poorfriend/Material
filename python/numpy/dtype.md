```python
import numpy as np
"""
See also in scipy.io.loadmat.md
"""
p = np.dtype([('name','S32'),('age','i'),('hometown','S32')])
person = np.dtype({'names':['name','age','hometown'],'formats':['S32','i','S32']})
print person
print p
"""
[('name', 'S32'), ('age', '<i4'), ('hometown', 'S32')]
[('name', 'S32'), ('age', '<i4'), ('hometown', 'S32')]
"""
group = np.array([[('fusheng', 21, 'sichuan'), ('kaka', 35, 'brazil')],
                  [('yufufu', 26, 'fujian') , ('libai', 1000, 'tang')]], dtype=person)
print group['name']
print group[0,0]
"""
[['fusheng' 'kaka']
 ['yufufu' 'libai']]
('fusheng', 21, 'sichuan')
"""
#获取属性名
print person.names
"""
('name', 'age', 'hometown')
""'
```

