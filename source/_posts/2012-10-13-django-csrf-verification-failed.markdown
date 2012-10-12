---
layout: post
title: "Django CSRF Verification Failed"
date: 2012-10-13 00:48
comments: true
categories: Programming
---  
一直想做一个iOS应用推送服务，但是不想用PHP，于是就想到了用Django来写。所以最近一直都在照着[Django Book](http://www.djangobook.com/)的教程学习。在学到第七章表单的时候遇到了一个问题。我按照教程上的内容，做了一个表单但是一点提交就出现如下的错误。  
![](../images/django/error.jpg)  
于是Google了一番，网上是这么说的：  
(1)在`<form>`里添加`{% csrf_token %}`：
	
	<form action="." method="post">
		{% csrf_token %}
		<table>
		{{ form.as_table }}
		</table>
		<input type="submit" value="Submit">
	</form>
(2)然后在seetings.py中找到MIDDLEWARE_CLASSES并且添加上  
	
	'django.middleware.csrf.CsrfViewMiddleware',
    'django.middleware.csrf.CsrfResponseMiddleware',
问题就能解决，但是我照做了之后发现以个更厉害的错误。提交表单之后，直接出现  `A server error occurred.  Please contact the administrator.`于是我就开始瞎捣鼓，我把`'django.middleware.csrf.CsrfResponseMiddleware',`给去掉以后，上面的这个错误没了，但是还是有CSRF的那个错误。我仔细看了一下错误的说明，发现第二条里提到了`RequestContext`这么个东西，于是点进去到官网上看，发现在使用Context渲染Template的时候，如果涉及到表单操作则需要使用`RequestContext`而不是`Context`。于是照着光网上的例子修改了代码：  
	
	return render_to_response('contact_form.html', {'form':form})
	要改成：
	c = RequestContext(request, {
            'form':form,
        })
    return render_to_response('contact_form.html', context_instance=c)
最终在views.py里应该是这样：
	
	def contact(request):
    	if request.method == 'POST':
        	form = ContactForm(request.POST)
        	if form.is_valid():
            	cd = form.cleaned_data
            	return HttpResponseRedirect('/thanks/')
    	else:
        	form = ContactForm(
                	initial = {'subject':'I love this one!'}
            	)
    	c = RequestContext(request, {
            	'form':form,
        	})
    	return render_to_response('contact_form.html', context_instance=c)
再测试一下就没问题了。总结一下，在出现CSRF的错误时，第一步在`<form>`里添加`{% csrf_token %}`，第二步在`render_to_response`里使用`RequestContext`对象，通过这两部就能解决这个错误。
