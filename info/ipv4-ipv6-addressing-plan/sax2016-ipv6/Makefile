.PHONY:	clean

all:	ipv6-slides.pdf


ipv6-slides.pdf:	ipv6-slides.txt
	pandoc -t beamer $< -o $@

clean:
	rm -f ipv6-slides.pdf
