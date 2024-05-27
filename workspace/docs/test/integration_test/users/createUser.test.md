import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  mockUsername2,
  mockPassword2,
  password,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'


export const userRegister = () => {
  describe('CREATE USER', () => {
    let superAdminId
    let userId
    let user2Id
    let user
    let token
    let userToken
    let adminId
    let adminToken

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user'
      }

      userId = await usersService.create(userInfo)

      delete userInfo.role

      userToken = await usersService.login(userInfo)

      const adminInfo = {
        username: randomUUID(),
        password: password,
        role: 'admin'
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)
    })

    afterAll(async () => {
      await usersService.delete(userId)
      await usersService.delete(superAdminId)
      await usersService.delete(user2Id)
      await usersService.delete(adminId)
    })

    beforeEach(() => {
      user = {
        username: mockUsername2,
        password: mockPassword2,
        role: 'user',
      }
    })

    it('Registration completed successfully', (done) => {
      try {
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            user2Id = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: user2Id,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('username is required', (done) => {
      try {
        delete user.username

        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USERNAME} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('username should not be empty', (done) => {
      try {
        user.username = ''
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USERNAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('username must be string', (done) => {
      user.username = 87876874564

      request
        .post('/api/users')
        .set('Authorization', `Bearer ${token}`)
        .send(user)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
            })
          )
          return done()
        })
    })

    it('username is duplicate', (done) => {
      try {
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.UNIQUE_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('users can not create a user', (done) => {
      try {
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${userToken}`)
          .send(user)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not create a user with superAdmin role', (done) => {
      try {
        user.role = 'superAdmin'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(user)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.NOT_ALLOW_ROLE}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not create a user with admin role', (done) => {
      try {
        user.role = 'admin'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(user)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.NOT_ALLOW_ROLE}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('password is required', (done) => {
      try {
        delete user.password
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('password should not be empty', (done) => {
      try {
        user.password = ''
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('password should not be lower than 8 characters', (done) => {
      try {

        user.password = 'aliA12@'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('password\'s pattern is not correct', (done) => {
      try {
        user.password = 'aliabdi123'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('username must be 5 characters atleast', (done) => {
      user.username = 'ali'

      request
        .post('/api/users')
        .set('Authorization', `Bearer ${token}`)
        .send(user)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
            })
          )
          return done()
        })
    })

    it('username must be maximum 36 characters', (done) => {
      user.username = 'aliaghapour_2022_tehran_partgroup_example'

      request
        .post('/api/users')
        .set('Authorization', `Bearer ${token}`)
        .send(user)
        .expect(400)

        .end(function (err, res) {
          if (err) return done(err)
          expect(res.body.data).toEqual(
            expect.objectContaining({
              status: 'error',
              message: `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
            })
          )
          return done()
        })
    })

    it('role is required', (done) => {
      try {
        delete user.role

        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ROLE} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('role can not be empty', (done) => {
      try {
        user.role = ''

        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: "role باید یکی از مقادیر  \"admin\", \"user\" باشد",
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('role can not be a number', (done) => {
      try {
        user.role = 46597644

        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: "role باید یکی از مقادیر  \"admin\", \"user\" باشد",
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('role must be admin or user', (done) => {
      try {
        user.role = 'operator'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: "role باید یکی از مقادیر  \"admin\", \"user\" باشد",
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('profile is incorrect', (done) => {
      try {
        user.profile = []
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROFILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('profile can not be a string', (done) => {
      try {
        user.profile = 'profile'
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROFILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('profile can not be a number', (done) => {
      try {
        user.profile = 54545754
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROFILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('profile can not be empty string', (done) => {
      try {
        user.profile = ''
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROFILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('field incorrect in profile', (done) => {
      try {
        user.profile = {
          username : 'username'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('firstName can not be an empty string', (done) => {
      try {
        user.profile = {
          firstName : ''
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FIRST_NAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('firstName can not be a number', (done) => {
      try {
        user.profile = {
          firstName : 79878
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('firstName can not be an object and it must be a string', (done) => {
      try {
        user.profile = {
          firstName : {}
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('lastName can not be an empty string', (done) => {
      try {
        user.profile = {
          lastName : ''
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.LAST_NAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('lastName can not be a number', (done) => {
      try {
        user.profile = {
          lastName : 79878
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('lastName can not be an object and it must be a string', (done) => {
      try {
        user.profile = {
          lastName : {}
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('email can not be an empty string', (done) => {
      try {
        user.profile = {
          email : ''
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EMAIL} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('email can not be a number', (done) => {
      try {
        user.profile = {
          email : 987987987
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EMAIL} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('email should be written in a correct format', (done) => {
      try {
        user.profile = {
          email : 'ali.aghapour@'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EMAIL} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('phoneNumber can not be an empty string', (done) => {
      try {
        user.profile = {
          phoneNumber : ''
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('phoneNumber can not be a number', (done) => {
      try {
        user.profile = {
          phoneNumber : 987987987
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('phoneNumber should be written in a correct format', (done) => {
      try {
        user.profile = {
          phoneNumber : '678+65*5'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('phoneNumber must be started with 0', (done) => {
      try {
        user.profile = {
          phoneNumber : '167806595'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('second digit in phoneNumber must match 9', (done) => {
      try {
        user.profile = {
          phoneNumber : '08166532090'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('phoneNumber must be 11 digits', (done) => {
      try {
        user.profile = {
          phoneNumber : '091665320'
        }
        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token =
          'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .post('/api/users')
          .set('Authorization', `Bearer ${token}`)
          .send(user)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .get('/api/users')
          .send(user)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
