---
title: Other Functions
weight: 3
layout: docs

---
The remaining functions found in the module 'Inbox Common.'

<hr />

#### **_Short-hand Wait_**

    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))

**A short-hand version of my preferred element selection method using Selenium.** 

<hr />

#### **_Queue Spellcheck_**

    def queue_spellcheck(q):
        if q.lower() not in lqlist:
            cur = 0
            for i in lqlist:
                rat = fuzz.partial_ratio(q.lower(),i)
                if rat > 80 and rat > cur:
                    best = i
                    cur = rat
            return best
        else:
            return q