import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  mockPassword3,
  password,
  mockPassword7,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'

export const userUpdate = () => {
  describe('UPDATE USER INFO', () => {
    let adminToken
    let userId
    let adminId
    let token
    let userToken
    let userInfo1
    let superAdminId
    let user_upd
    let userId2

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const adminInfo = {
          username: randomUUID(),
          password: password,
          role: 'admin'
        }

        adminId = await usersService.create(adminInfo)

        delete adminInfo.role

        adminToken = await usersService.login(adminInfo)

        userInfo1 = {
          username: randomUUID(),
          password: mockPassword3,
          role: 'user',
        }

        userId = await usersService.create(userInfo1)

        delete userInfo1.role

        userToken = await usersService.login(userInfo1)

        const userInfo2 = {
          username: randomUUID(),
          password: mockPassword7,
          role: 'user',
        }

        userId2 = await usersService.create(userInfo2)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await usersService.delete(userId)
      await usersService.delete(userId2)
      await usersService.delete(superAdminId)
      await usersService.delete(adminId)
    })

    beforeEach(() => {
      user_upd = {
        profile: {
          firstName: 'ali',
          lastName: 'aghapour',
        },
        role: 'user',
      }
    })

    it('update user completed successfully', (done) => {
      try {
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not update roles', (done) => {
      try {
        user_upd.role = 'user'

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(user_upd)
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

    it('admins can update no one but users', (done) => {
      try {
        delete user_upd.role
        user_upd.profile.firstName = 'alii'

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(user_upd)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('users can just update themselves', (done) => {
      try {
        delete user_upd.role

        request
          .patch(`/api/users/info/${userId2}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(user_upd)
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

    it('profile must be object', (done) => {
      try {
        user_upd.profile = '7888888'

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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

    it('username is duplicate', (done) => {
      try {
        user_upd.username = userInfo1.username

        request
          .patch(`/api/users/info/${userId2}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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

    it('role must be admin or user', (done) => {
      try {
        user_upd.role = 'operator'

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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

    it('role can not be empty', (done) => {
      try {
        user_upd.role = ''

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.role = 46597644

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = []
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = 'profile'
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = 54545754

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = ''

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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

    it('unauthorized user can not create a user', (done) => {
      try {
        request
          .patch(`/api/users/info/${userId2}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(user_upd)
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

    it('username must be 5 characters atleast', (done) => {
      user_upd.username = 'ali'

      request
        .patch(`/api/users/info/${userId}`)
        .set('Authorization', `Bearer ${token}`)
        .send(user_upd)
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
      user_upd.username = 'aliaghapour_2022_tehran_partgroup_example'

      request
        .patch(`/api/users/info/${userId}`)
        .set('Authorization', `Bearer ${token}`)
        .send(user_upd)
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

    it('field incorrect in profile', (done) => {
      try {
        user_upd.profile = {
          username : 'username'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          firstName : ''
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          firstName : 79878
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          firstName : {}
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          lastName : ''
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          lastName : 79878
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          lastName : {}
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          email : ''
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          email : 987987987
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          email : 'ali.aghapour@'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : ''
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : 987987987
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : '678+65*5'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : '167806595'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : '08166532090'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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
        user_upd.profile = {
          phoneNumber : '091665320'
        }
        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
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

    it('userId is not uuid', (done) => {
      try {
        const wrongUserId = 'kjh23j4h2jh34'

        request
          .patch(`/api/users/info/${wrongUserId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(user_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .patch(`/api/users/info/${userId}`)
          .set('Authorization', `Bearer ${expiredToken}`)
          .send(user_upd)
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
          .patch(`/api/users/info/${userId}`)
          .send(user_upd)
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
