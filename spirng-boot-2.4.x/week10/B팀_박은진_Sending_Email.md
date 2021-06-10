## **18. Sending Email**

<br>

### JavaMailSender interface

- 이메일 전송을 위한 <code>JavaMailSender</code> interface 추상화 제공
- starter module과 auto-configuration 제공
- (spring) JavaMailSender Bean 생성 없이 properties 설정만으로 사용 가능

<br>

### MailProperties

- spring.mail 네임스페이스 설정으로 host, port 등

<details>
<summary>application.properties</summary>
<div markdown="1">

spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=이메일
spring.mail.password=비밀번호
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true

</div>
</details>

- 기본 timeout은 무한
- Mail 서버에 의해 thread가 차단되지 않도록 변경 가능

<br>

<details>
<summary>MailHandler.java</summary>
<div markdown="1">

```java
package com.project.triport.util;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.IOUtils;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;

public class MailHandler {
    private JavaMailSender sender;
    private MimeMessage mimeMessage;
    private MimeMessageHelper messageHelper;

    // 생성자
    public MailHandler(JavaMailSender javaMailSender) throws MessagingException {
        this.sender = javaMailSender;
        mimeMessage = javaMailSender.createMimeMessage();
        messageHelper = new MimeMessageHelper(mimeMessage, true, "UTF-8");
    }

    // 보내는 사람
    public void setFrom(String fromAddress) throws MessagingException {
        messageHelper.setFrom(fromAddress);
    }

    // 받는 사람
    public void setTo(String email) throws MessagingException {
        messageHelper.setTo(email);
    }

    // 제목
    public void setSubject(String subject) throws MessagingException {
        messageHelper.setSubject(subject);
    }

    // 메일 내용
    public void setText(String text, boolean useHtml) throws MessagingException {
        messageHelper.setText(text, useHtml);
    }

    // 이미지 삽입
    public void setInline(String contentId, String pathToInline) throws MessagingException, IOException {
//        File file = new ClassPathResource(pathToInline).getFile();
//        FileSystemResource fileSystemResource = new FileSystemResource(file);

        ClassPathResource classPathResource = new ClassPathResource(pathToInline);
        InputStream inputStream = classPathResource.getInputStream();
        File file = File.createTempFile("file", ".png");

        try {
            FileUtils.copyInputStreamToFile(inputStream, file);
        } finally {
            IOUtils.closeQuietly(inputStream);
        }

        messageHelper.addInline(contentId, file);
    }

    // 발송
    public void send() {
        try {
            sender.send(mimeMessage);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}

```

</div>
</details>

<details>
<summary>MailUtil.java</summary>
<div markdown="1">

```java
package com.project.triport.util;

import com.project.triport.entity.Member;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

import javax.mail.MessagingException;
import java.io.IOException;

@Service
@RequiredArgsConstructor
public class MailUtil {

    private final JavaMailSender mailSender;
    private @Value("${spring.mail.username}") String fromMail;


    // *임시 비밀번호 Mail 발송
    @Async
    public void TempPwdMail(Member member, String tmpPwd) throws IOException, MessagingException {

        MailHandler mailHandler = new MailHandler(mailSender);
        String nickname = member.getNickname();

        // 임시 비밀번호 Mail 내용
        // 받는 사람
        mailHandler.setTo(member.getEmail());
        // 보내는 사람
        mailHandler.setFrom(fromMail);
        // 제목
        mailHandler.setSubject("[TRIPORT] "+nickname+"님의 임시 비밀번호를 확인해 주세요.");
        // 내용 (HTML Layout)
        String htmlContent = "이메일 내용";
        mailHandler.setText(htmlContent, true);
        // 이미지 삽입
        mailHandler.setInline("tripper_with_logo", "static/tripper_with_logo.png");

        mailHandler.send();
    }

    // *Trils LikeNum 5개인 member에게 promotion 메일 발송
    @Async
    public void trilsPromoMail(Member member) throws MessagingException, IOException {
        MailHandler mailHandler = new MailHandler(mailSender);
        String nickname = member.getNickname();

        // 받는 사람
        mailHandler.setTo(member.getEmail());
        // 보내는 사람
        mailHandler.setFrom(fromMail);
        // 제목
        mailHandler.setSubject("[TRIPORT] 회원님의 Trils 영상이 HOT 해요! 😍");
        // 내용 (HTML Layout)
        String htmlContent ="이메일 내용";
        mailHandler.setText(htmlContent, true);
        // 이미지 삽입
        mailHandler.setInline("triport_logo", "static/triport_logo.png");
        mailHandler.setInline("trils_promo", "static/trils_promo.png");

        mailHandler.send();
    }
}
```

</div>
</details>

