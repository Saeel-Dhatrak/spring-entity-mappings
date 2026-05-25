### One to One Relationship
- Every user in a socialmedia platform is going to have a profile. And one user can hav one profile at max. So it's like 1 to 1 relationship for USer to Profile and same 1 to 1 relationship for Profile to User.
- ```java
    import jakarta.persistence.Entity;
    import jakarta.persistence.GeneratedValue;
    import jakarta.persistence.GenerationType;
    import jakarta.persistence.Id;

    @Entity
    public class SocialUser {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    }
    //------------------------------------------------------------//
    import jakarta.persistence.*;

    @Entity
    public class SocialProfile {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne
        private SocialUser user;
    }
  ```
- Upon doing this we will have two tale created in the database.
    - Social_User table with just one column "ID"
    - Social_Profile tabel with two columns "ID" and "USER_ID"
- "USER_ID" is a link or a relationship to that of the user table from the profile table.
- "USER_ID" is a foreign key to the Social_User Table.
- We can hvae the name of the "USER_ID" changed with the help of @JoinColumn annotation
- ```java
    @OneToOne
    @JoinColumn(name = "social_user")
    private SocialUser user;
  ```
- Now the Social_Profile tabel with two columns "ID" and "SOCIAL_USER"

### Bi-directional One to One Relationship
- Right now only the social profile class is aware of this relationship because we have added the OneToOne maping annotation inside the SocialProfile class only.
- Suppose that a User can have a profile containing additional information like the biography, contact detais and so on.
- In that particualr case we might want to access the profile information from the social user class.
- But right now the social user class does not know about the existing reltionship.
- ```java
    import jakarta.persistence.*;

    @Entity
    public class SocialUser {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne
        private SocialProfile socialProfile;
    }
  ```
- Now we will have:
    - Social_User table with just two columns "ID" and "SOCIAL_PROFILE_ID"
    - Social_Profile tabel with two columns "ID" and "USER_ID"
- This will lead to redundancy as we will have error while using the queries like which foreign key to use.
- So we will make the SOCIAL_USER" as the owner of the relationship. SO in the Social_USer class we can have the @JoinCoulmn annotation with name of the column that has been added in the dataase explicitly i.e "social_profile_id"
- ```java
    @Entity
    public class SocialUser {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne
        @JoinColumn(name = "social_profile_id")
        private SocialProfile socialProfile;
    }
  ```
- Next we do not need a foreign key in the social_profile table that is representing the user. For this we will mae use of the mappedBy attribute along with the field name present in the Social_User class i.e social_profile.
- ```java
    @Entity
    public class SocialProfile {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne(mappedBy = "socialProfile")
        //@JoinColumn(name = "social_user")
        private SocialUser user;
    }
  ```
- Looking at this JPA will know that their is a relationship with SocialUser : it's a 1 to 1 relationship : mappedBy socialProfile field which exist in the SocialUser class. Since their is no forign key we will comment out the @JoinColumn annotation
- Now in the database we will have:
    - Social_User table with just two columns "ID" and "SOCIAL_PROFILE_ID"
    - Social_Profile tabel with only one column "ID"
- So this is a bi-dirctional relationship but the SocialUser class is the owner of the relationship and is managing the reatioship with the help of the "socialProfile" field over here.
- SO the non owing side have to make use of the mappedBy to specify that this relationship is already being mapped and managed by the specified field, So don't create a column over here.
- And the @JoinColumn is used only by the owning side to specify the join column name for joining the table.
- If we want to make the SocialProfile as the owner then:
- ```java
    @Entity
    public class SocialProfile {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne
        @JoinColumn(name = "social_user")
        private SocialUser user;
    }
    //------------------------------------------------------------//
    @Entity
    public class SocialUser {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @OneToOne(mappedBy = "user")
        //@JoinColumn(name = "social_profile_id")
        private SocialProfile socialProfile;
    }
  ```
- So here we flipped the annoatations. Now the database will have :
    - Social_User table with just one columns "ID"
    - Social_Profile tabel with two columns "ID" and "SOCIAL_USER"