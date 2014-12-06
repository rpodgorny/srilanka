#!/usr/bin/python

from __future__ import division

import alsaaudio
import array
import numpy
import numpy.fft
import sys

pcm = alsaaudio.PCM(type=alsaaudio.PCM_CAPTURE)
pcm.setchannels(1)
pcm.setrate(16000)
#pcm.setperiodsize(16)
w = 60

maxi = 1
while 1:
	maxi *= 0.99
	a = None
	pakos = 3
	length, data = pcm.read()
	a = array.array('h', data)
	#while not a or len(a) < int(1000/pakos):
	#	length, data = pcm.read()
	#	if not a:
	#		a = array.array('h', data)
	#	else:
	#		a += array.array('h', data)
	#print length, '%010d' % sum(a)
	ff = numpy.fft.rfft(a)
	#print len(a), len(ff)
	div = 2
	v = 1
	for i in range(len(ff)):
		if i%div == 0:
			tmp = v/maxi
			if tmp < 0.05: sys.stdout.write(' ')
			elif tmp < 0.1: sys.stdout.write('.')
			elif tmp < 0.2: sys.stdout.write(';')
			elif tmp < 0.3: sys.stdout.write('|')
			else: sys.stdout.write('#')
			v = 0
		#endif
		#v += abs(numpy.real(ff[i]))
		v += abs(numpy.real(ff[i]))
		if v > maxi: maxi = v
	#endfor
	print '|', maxi
#endwhile

pcm.close()
